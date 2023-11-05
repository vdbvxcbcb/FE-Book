前端会有一份路由表，它表示了每一个路由可访问的权限。当用户登录之后，通过 **token** 获取用户的 **role** ，

动态根据用户的 **role** 算出其对应有权限的路由，再通过`router.addRoutes`动态挂载路由。

但这些控制都只是页面级的，说白了前端再怎么做权限控制都不是绝对安全的，后端的权限验证是逃不掉的。

后端则会验证每一个涉及请求的操作，验证其是否有该操作的权限，

每一个后台的请求不管是 get 还是 post 都会让前端在请求 `header`里面携带用户的 **token**，后端会根据该 **token** 来验证用户是否有权限执行该操作。

若没有权限则抛出一个对应的状态码，前端检测到该状态码，做出相对应的操作。

权限 前端or后端 来控制？

有很多人表示他们公司的路由表是于后端根据用户的权限动态生成的，不采取这种方式的原因如下：

项目不断的迭代你会异常痛苦，前端新开发一个页面还要让后端配一下路由和权限，让我们想了曾经前后端不分离，被后端支配的那段恐怖时间了。

其次，就拿业务来说，虽然后端的确也是有权限验证的，但它的验证其实是针对业务来划分的，

比如超级编辑可以发布文章，而实习编辑只能编辑文章不能发布，但对于前端来说不管是超级编辑还是实习编辑都是有权限进入文章编辑页面的。

所以前端和后端权限的划分是不太一致。

具体实现

1. 创建 vue 实例的时候将 vue-router 挂载，但这个时候 vue-router 挂载一些登录或者不用权限的公用的页面。
2. 当用户登录后，获取用 role，将 role 和路由表每个页面的需要的权限作比较，生成最终用户可访问的路由表。
3. 调用 router.addRoutes(store.getters.addRouters) 添加用户可访问的路由。
4. 使用 vuex 管理路由表，根据 vuex 中可访问的路由渲染侧边栏组件。

click事件触发登录操作:

```js
    handleLogin() {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true
          // 因为 module 设置了 namespaced: true ，所以可以 this.$store.dispatch('user/login')
          this.$store.dispatch('user/login', this.loginForm)
            .then(() => {
              this.$router.push({ path: this.redirect || '/', query: this.otherQuery })
              this.loading = false
            })
            .catch(() => {
              this.loading = false
            })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
```

src\store\modules\user.js

```js
  login({ commit }, userInfo) {
    const { username, password } = userInfo
    return new Promise((resolve, reject) => {
      login({ username: username.trim(), password: password }).then(response => {
        const { data } = response
        commit('SET_TOKEN', data.token)
        setToken(data.token)
        resolve()
      }).catch(error => {
        reject(error)
      })
    })
  },
```

登录成功后，服务端会返回一个 token（该token的是一个能唯一标示用户身份的一个key），

之后将 token 存储在本地 cookie 之中，这样下次打开页面或者刷新页面的时候能记住用户的登录状态，不用再去登录页面重新登录

根据 token 再去拉取一个 user_info 的接口来获取用户的详细信息（如用户权限，用户名等等信息）。

通过 axios 封装一个 request ，在拦截器在每个请求头里面塞入token，好让后端对请求进行权限验证。

并创建一个 respone 拦截器，当服务端返回特殊的状态码，我们统一做处理，如没权限或者 token 失效等操作。

src\utils\request.js

```js
import axios from 'axios'
import { MessageBox, Message } from 'element-ui'
import store from '@/store'
import { getToken } from '@/utils/auth'

// create an axios instance
const service = axios.create({
  baseURL: process.env.VUE_APP_BASE_API, // url = base url + request url
  // withCredentials: true, // send cookies when cross-domain requests
  timeout: 5000 // request timeout
})

// request interceptor
service.interceptors.request.use(
  config => {
    // do something before request is sent

    if (store.getters.token) {
      // let each request carry token
      // ['X-Token'] is a custom headers key
      // please modify it according to the actual situation
      config.headers['X-Token'] = getToken()
    }
    return config
  },
  error => {
    // do something with request error
    console.log(error) // for debug
    return Promise.reject(error)
  }
)

// response interceptor
service.interceptors.response.use(
  /**
   * If you want to get http information such as headers or status
   * Please return  response => response
  */

  /**
   * Determine the request status by custom code
   * Here is just an example
   * You can also judge the status by HTTP Status Code
   */
  response => {
    const res = response.data

    // if the custom code is not 20000, it is judged as an error.
    if (res.code !== 20000) {
      Message({
        message: res.message || 'Error',
        type: 'error',
        duration: 5 * 1000
      })

      // 50008: Illegal token; 50012: Other clients logged in; 50014: Token expired;
      if (res.code === 50008 || res.code === 50012 || res.code === 50014) {
        // to re-login
        MessageBox.confirm('You have been logged out, you can cancel to stay on this page, or log in again', 'Confirm logout', {
          confirmButtonText: 'Re-Login',
          cancelButtonText: 'Cancel',
          type: 'warning'
        }).then(() => {
          store.dispatch('user/resetToken').then(() => {
            location.reload()
          })
        })
      }
      return Promise.reject(new Error(res.message || 'Error'))
    } else {
      return res
    }
  },
  error => {
    console.log('err' + error) // for debug
    Message({
      message: error.message,
      type: 'error',
      duration: 5 * 1000
    })
    return Promise.reject(error)
  }
)

export default service
```

**关键的**

src\permission.js 

```js
router.beforeEach(async(to, from, next) => {
  // start progress bar
  NProgress.start()

  // set page title
  document.title = getPageTitle(to.meta.title)

  // determine whether the user has logged in // 判断是否有token
  const hasToken = getToken()

  if (hasToken) {
    if (to.path === '/login') {
      // if is logged in, redirect to the home page 登录过直接重定向到主页
      next({ path: '/' })
      NProgress.done() // hack: https://github.com/PanJiaChen/vue-element-admin/pull/2939
    } else {
      // determine whether the user has obtained his permission roles through getInfo
      const hasRoles = store.getters.roles && store.getters.roles.length > 0 // 判断当前用户是否已拉取完user_info信息
      if (hasRoles) {
        next() // 当有用户权限的时候，说明所有可访问路由已生成 如访问没权限的页面会自动进入404页面
      } else {
        try {
          // get user info
          // note: roles must be a object array! such as: ['admin'] or ,['developer','editor']
          const { roles } = await store.dispatch('user/getInfo')  // 拉取用户 info

          // generate accessible routes map based on roles
          const accessRoutes = await store.dispatch('permission/generateRoutes', roles) // 根据角色生成可访问的路由表

          // dynamically add accessible routes
          router.addRoutes(accessRoutes)       // 动态添加可访问路由表
          
          // router.addRoutes之后的 next() 可能会失效，因为可能 next() 的时候路由并没有完全 add 完成  
          // hack method to ensure that addRoutes is complete
          // set the replace: true, so the navigation will not leave a history record
          // hack 方法 确保 addRoutes 已完成 
          // 通过 next(to) 巧妙避开之前的那个问题。
          // 这行代码重新进入router.beforeEach这个钩子，这时候再通过next()来释放钩子，就能确保所有的路由都已经挂载完成了。
          next({ ...to, replace: true })
        } catch (error) {
          // remove token and go to login page to re-login
          await store.dispatch('user/resetToken')
          Message.error(error || 'Has Error')
          next(`/login?redirect=${to.path}`)
          NProgress.done()
        }
      }
    }
  } else {
    /* has no token*/

    if (whiteList.indexOf(to.path) !== -1) {   
      // in the free login whitelist, go directly // 在免登录白名单，直接进入
      next()
    } else { // 否则全部重定向到登录页
      // other pages that do not have permission to access are redirected to the login page.
      next(`/login?redirect=${to.path}`)
      NProgress.done()
    }
  }
})
```

src\store\modules\permission.js

这里的代码说白了就是干了一件事，

通过用户的权限和在 router.js 里面 asyncRoutes 的每一个页面所需要的权限做匹配，最后返回一个该用户能够访问路由有哪些。

```js
import { asyncRoutes, constantRoutes } from '@/router'

/**
 * Use meta.role to determine if the current user has permission
 * @param roles
 * @param route
 */
function hasPermission(roles, route) {
  if (route.meta && route.meta.roles) {
    return roles.some(role => route.meta.roles.includes(role))
  } else {
    return true
  }
}

/**
 * Filter asynchronous routing tables by recursion
 * @param routes asyncRoutes
 * @param roles
 */
export function filterAsyncRoutes(routes, roles) {
  const res = []

  routes.forEach(route => {
    const tmp = { ...route }
    if (hasPermission(roles, tmp)) {
      if (tmp.children) {
        tmp.children = filterAsyncRoutes(tmp.children, roles)
      }
      res.push(tmp)
    }
  })

  return res
}

const state = {
  routes: [],
  addRoutes: []
}

const mutations = {
  SET_ROUTES: (state, routes) => {
    state.addRoutes = routes
    state.routes = constantRoutes.concat(routes)
  }
}

const actions = {
  generateRoutes({ commit }, roles) {
    return new Promise(resolve => {
      let accessedRoutes
      // 如果是管理员则无需过滤权限路由表
      if (roles.includes('admin')) {
        accessedRoutes = asyncRoutes || []
      } else { // 否则根据用户角色过滤生成对应的权限路由表
        accessedRoutes = filterAsyncRoutes(asyncRoutes, roles)
      }
      // Vuex 设置根据权限动态生成的路由表
      commit('SET_ROUTES', accessedRoutes)
      resolve(accessedRoutes)
    })
  }
}

export default {
  namespaced: true,
  state,
  mutations,
  actions
}
```

src/store/getters.js

```js
const getters = {
  sidebar: state => state.app.sidebar,
  size: state => state.app.size,
  device: state => state.app.device,
  visitedViews: state => state.tagsView.visitedViews,
  cachedViews: state => state.tagsView.cachedViews,
  token: state => state.user.token,
  avatar: state => state.user.avatar,
  name: state => state.user.name,
  introduction: state => state.user.introduction,
  roles: state => state.user.roles,
  permission_routes: state => state.permission.routes,
  errorLogs: state => state.errorLog.logs
}

export default getters
```

src/store/index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'
import getters from './getters'

Vue.use(Vuex)

// https://webpack.js.org/guides/dependency-management/#requirecontext
const modulesFiles = require.context('./modules', true, /\.js$/)

// you do not need `import app from './modules/app'`
// it will auto require all vuex module from modules file
const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default
  return modules
}, {})

const store = new Vuex.Store({
  modules,
  getters
})

export default store
```

src\router\index.js

实现 router.js 路由表，这里就拿前端控制路由来举例(后端存储的也差不多，稍微改造一下就好了)

vue-router 挂载一些登录或者不用权限的公用的页面 

constantRoutes  所有权限通用路由表 ，如首页和登录页和一些不用权限的公用页面

asyncRoutes       异步挂载的路由，动态需要根据权限加载的路由表 

通过 token 获取用户对应的 role，动态根据用户的 role 算出其对应有权限的路由，通过 router.addRoutes 动态挂载这些路由。

vue-router官方推荐 的方法通过 meta 标签来标示改页面能访问的权限有哪些。

如 meta: { role: ['admin','super_editor'] } 表示该页面只有 admin 和超级编辑才能有资格进入。

这里有一个需要非常注意的地方就是 `404` 页面一定要最后加载，如果放在`constantRouterMap`一同声明了`404`，后面的所以页面都会被拦截到`404`

```js
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

/* Layout */
import Layout from '@/layout'

/* Router Modules */
import componentsRouter from './modules/components'
import chartsRouter from './modules/charts'
import tableRouter from './modules/table'
import nestedRouter from './modules/nested'


/**
 * constantRoutes
 * a base page that does not have permission requirements
 * all roles can be accessed
 */
export const constantRoutes = [
  {
    path: '/redirect',
    component: Layout,
    hidden: true,
    children: [
      {
        path: '/redirect/:path(.*)',
        component: () => import('@/views/redirect/index')
      }
    ]
  },
  {
    path: '/login',
    component: () => import('@/views/login/index'),
    hidden: true
  },
  {
    path: '/auth-redirect',
    component: () => import('@/views/login/auth-redirect'),
    hidden: true
  },
  {
    path: '/404',
    component: () => import('@/views/error-page/404'),
    hidden: true
  },
  {
    path: '/401',
    component: () => import('@/views/error-page/401'),
    hidden: true
  },
  {
    path: '/',
    component: Layout,
    redirect: '/dashboard',
    children: [
      {
        path: 'dashboard',
        component: () => import('@/views/dashboard/index'),
        name: 'Dashboard',
        meta: { title: 'dashboard', icon: 'dashboard', affix: true }
      }
    ]
  },
  {
    path: '/documentation',
    component: Layout,
    children: [
      {
        path: 'index',
        component: () => import('@/views/documentation/index'),
        name: 'Documentation',
        meta: { title: 'documentation', icon: 'documentation', affix: true }
      }
    ]
  },
  {
    path: '/guide',
    component: Layout,
    redirect: '/guide/index',
    children: [
      {
        path: 'index',
        component: () => import('@/views/guide/index'),
        name: 'Guide',
        meta: { title: 'guide', icon: 'guide', noCache: true }
      }
    ]
  },
  {
    path: '/profile',
    component: Layout,
    redirect: '/profile/index',
    hidden: true,
    children: [
      {
        path: 'index',
        component: () => import('@/views/profile/index'),
        name: 'Profile',
        meta: { title: 'profile', icon: 'user', noCache: true }
      }
    ]
  }
]

/**
 * asyncRoutes
 * the routes that need to be dynamically loaded based on user roles
 */
export const asyncRoutes = [
  {
    path: '/permission',
    component: Layout,
    redirect: '/permission/page',
    alwaysShow: true, // will always show the root menu
    name: 'Permission',
    meta: {
      title: 'permission',
      icon: 'lock',
      roles: ['admin', 'editor'] // you can set roles in root nav
    },
    children: [
      {
        path: 'page',
        component: () => import('@/views/permission/page'),
        name: 'PagePermission',
        meta: {
          title: 'pagePermission',
          roles: ['admin'] // or you can only set roles in sub nav
        }
      },
      {
        path: 'directive',
        component: () => import('@/views/permission/directive'),
        name: 'DirectivePermission',
        meta: {
          title: 'directivePermission'
          // if do not set roles, means: this page does not require permission
        }
      },
      {
        path: 'role',
        component: () => import('@/views/permission/role'),
        name: 'RolePermission',
        meta: {
          title: 'rolePermission',
          roles: ['admin']
        }
      }
    ]
  },

  {
    path: '/icon',
    component: Layout,
    children: [
      {
        path: 'index',
        component: () => import('@/views/icons/index'),
        name: 'Icons',
        meta: { title: 'icons', icon: 'icon', noCache: true }
      }
    ]
  },

  /** when your routing map is too long, you can split it into small modules **/
  componentsRouter,
  chartsRouter,
  nestedRouter,
  tableRouter,

  {
    path: '/example',
    component: Layout,
    redirect: '/example/list',
    name: 'Example',
    meta: {
      title: 'example',
      icon: 'el-icon-s-help'
    },
    children: [
      {
        path: 'create',
        component: () => import('@/views/example/create'),
        name: 'CreateArticle',
        meta: { title: 'createArticle', icon: 'edit' }
      },
      {
        path: 'edit/:id(\\d+)',
        component: () => import('@/views/example/edit'),
        name: 'EditArticle',
        meta: { title: 'editArticle', noCache: true, activeMenu: '/example/list' },
        hidden: true
      },
      {
        path: 'list',
        component: () => import('@/views/example/list'),
        name: 'ArticleList',
        meta: { title: 'articleList', icon: 'list' }
      }
    ]
  },

  {
    path: '/tab',
    component: Layout,
    children: [
      {
        path: 'index',
        component: () => import('@/views/tab/index'),
        name: 'Tab',
        meta: { title: 'tab', icon: 'tab' }
      }
    ]
  },

  {
    path: '/error',
    component: Layout,
    redirect: 'noRedirect',
    name: 'ErrorPages',
    meta: {
      title: 'errorPages',
      icon: '404'
    },
    children: [
      {
        path: '401',
        component: () => import('@/views/error-page/401'),
        name: 'Page401',
        meta: { title: 'page401', noCache: true }
      },
      {
        path: '404',
        component: () => import('@/views/error-page/404'),
        name: 'Page404',
        meta: { title: 'page404', noCache: true }
      }
    ]
  },

  {
    path: '/error-log',
    component: Layout,
    children: [
      {
        path: 'log',
        component: () => import('@/views/error-log/index'),
        name: 'ErrorLog',
        meta: { title: 'errorLog', icon: 'bug' }
      }
    ]
  },

  {
    path: '/excel',
    component: Layout,
    redirect: '/excel/export-excel',
    name: 'Excel',
    meta: {
      title: 'excel',
      icon: 'excel'
    },
    children: [
      {
        path: 'export-excel',
        component: () => import('@/views/excel/export-excel'),
        name: 'ExportExcel',
        meta: { title: 'exportExcel' }
      },
      {
        path: 'export-selected-excel',
        component: () => import('@/views/excel/select-excel'),
        name: 'SelectExcel',
        meta: { title: 'selectExcel' }
      },
      {
        path: 'export-merge-header',
        component: () => import('@/views/excel/merge-header'),
        name: 'MergeHeader',
        meta: { title: 'mergeHeader' }
      },
      {
        path: 'upload-excel',
        component: () => import('@/views/excel/upload-excel'),
        name: 'UploadExcel',
        meta: { title: 'uploadExcel' }
      }
    ]
  },

  {
    path: '/zip',
    component: Layout,
    redirect: '/zip/download',
    alwaysShow: true,
    name: 'Zip',
    meta: { title: 'zip', icon: 'zip' },
    children: [
      {
        path: 'download',
        component: () => import('@/views/zip/index'),
        name: 'ExportZip',
        meta: { title: 'exportZip' }
      }
    ]
  },

  {
    path: '/pdf',
    component: Layout,
    redirect: '/pdf/index',
    children: [
      {
        path: 'index',
        component: () => import('@/views/pdf/index'),
        name: 'PDF',
        meta: { title: 'pdf', icon: 'pdf' }
      }
    ]
  },
  {
    path: '/pdf/download',
    component: () => import('@/views/pdf/download'),
    hidden: true
  },

  {
    path: '/theme',
    component: Layout,
    children: [
      {
        path: 'index',
        component: () => import('@/views/theme/index'),
        name: 'Theme',
        meta: { title: 'theme', icon: 'theme' }
      }
    ]
  },

  {
    path: '/clipboard',
    component: Layout,
    children: [
      {
        path: 'index',
        component: () => import('@/views/clipboard/index'),
        name: 'ClipboardDemo',
        meta: { title: 'clipboardDemo', icon: 'clipboard' }
      }
    ]
  },

  {
    path: '/i18n',
    component: Layout,
    children: [
      {
        path: 'index',
        component: () => import('@/views/i18n-demo/index'),
        name: 'I18n',
        meta: { title: 'i18n', icon: 'international' }
      }
    ]
  },

  {
    path: 'external-link',
    component: Layout,
    children: [
      {
        path: 'https://github.com/PanJiaChen/vue-element-admin',
        meta: { title: 'externalLink', icon: 'link' }
      }
    ]
  },

  // 404 page must be placed at the end !!!
  { path: '*', redirect: '/404', hidden: true }
]

const createRouter = () => new Router({
  // mode: 'history', // require service support
  scrollBehavior: () => ({ y: 0 }),
  routes: constantRoutes
})

const router = createRouter()

// Detail see: https://github.com/vuejs/vue-router/issues/1234#issuecomment-357941465
export function resetRouter() {
  const newRouter = createRouter()
  router.matcher = newRouter.matcher // reset router
}

export default router
```

src/layout/components/Sidebar/SidebarItem.vue

侧边栏说白了就是遍历之前算出来的`permission_routers`，通过 vuex 拿到之后动态 v-for 渲染而已。

`permission_routers` 其实就是 src\store\modules\permission.js 这里的 routes

```js
const state = {
  routes: [],
  addRoutes: []
}
```

不过这里因为有一些业务需求所以加了很多判断 比如我们在定义路由的时候会加很多参数

```xquery
/**
* hidden: true                   if `hidden:true` will not show in the sidebar(default is false)
* redirect: noredirect           if `redirect:noredirect` will no redirct in the breadcrumb
* name:'router-name'             the name is used by <keep-alive> (must set!!!)
* meta : {
   role: ['admin','editor']     will control the page role (you can set multiple roles)
   title: 'title'               the name show in submenu and breadcrumb (recommend set)
   icon: 'svg-name'             the icon show in the sidebar,
   noCache: true                if fasle ,the page will no be cached(default is false)
 }
**/
```

```vue
<template>
  <div :class="{'has-logo':showLogo}">
    <logo v-if="showLogo" :collapse="isCollapse" />
    <el-scrollbar wrap-class="scrollbar-wrapper">
      <el-menu
        :default-active="activeMenu"
        :collapse="isCollapse"
        :background-color="variables.menuBg"
        :text-color="variables.menuText"
        :unique-opened="false"
        :active-text-color="variables.menuActiveText"
        :collapse-transition="false"
        mode="vertical"
      >
        <sidebar-item v-for="route in permission_routes" :key="route.path" :item="route" :base-path="route.path" />
      </el-menu>
    </el-scrollbar>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import Logo from './Logo'
import SidebarItem from './SidebarItem'
import variables from '@/styles/variables.scss'

export default {
  components: { SidebarItem, Logo },
  computed: {
    ...mapGetters([
      'permission_routes',
      'sidebar'
    ]),
    activeMenu() {
      const route = this.$route
      const { meta, path } = route
      // if set path, the sidebar will highlight the path you set
      if (meta.activeMenu) {
        return meta.activeMenu
      }
      return path
    },
    showLogo() {
      return this.$store.state.settings.sidebarLogo
    },
    variables() {
      return variables
    },
    isCollapse() {
      return !this.sidebar.opened
    }
  }
}
</script>
```

src/layout/components/Sidebar/SidebarItem.vue

```vue
<template>
  <div v-if="!item.hidden">
    <template v-if="hasOneShowingChild(item.children,item) && (!onlyOneChild.children||onlyOneChild.noShowingChildren)&&!item.alwaysShow">
      <app-link v-if="onlyOneChild.meta" :to="resolvePath(onlyOneChild.path)">
        <el-menu-item :index="resolvePath(onlyOneChild.path)" :class="{'submenu-title-noDropdown':!isNest}">
          <item :icon="onlyOneChild.meta.icon||(item.meta&&item.meta.icon)" :title="onlyOneChild.meta.title" />
        </el-menu-item>
      </app-link>
    </template>

    <el-submenu v-else ref="subMenu" :index="resolvePath(item.path)" popper-append-to-body>
      <template slot="title">
        <item v-if="item.meta" :icon="item.meta && item.meta.icon" :title="item.meta.title" />
      </template>
      <sidebar-item
        v-for="child in item.children"
        :key="child.path"
        :is-nest="true"
        :item="child"
        :base-path="resolvePath(child.path)"
        class="nest-menu"
      />
    </el-submenu>
  </div>
</template>

<script>
import path from 'path'
import { isExternal } from '@/utils/validate'
import Item from './Item'
import AppLink from './Link'
import FixiOSBug from './FixiOSBug'

export default {
  name: 'SidebarItem',
  components: { Item, AppLink },
  mixins: [FixiOSBug],
  props: {
    // route object
    item: {
      type: Object,
      required: true
    },
    isNest: {
      type: Boolean,
      default: false
    },
    basePath: {
      type: String,
      default: ''
    }
  },
  data() {
    // To fix https://github.com/PanJiaChen/vue-admin-template/issues/237
    // TODO: refactor with render function
    this.onlyOneChild = null
    return {}
  },
  methods: {
    hasOneShowingChild(children = [], parent) {
      const showingChildren = children.filter(item => {
        if (item.hidden) {
          return false
        } else {
          // Temp set(will be used if only has one showing child)
          this.onlyOneChild = item
          return true
        }
      })

      // When there is only one child router, the child router is displayed by default
      if (showingChildren.length === 1) {
        return true
      }

      // Show parent if there are no child router to display
      if (showingChildren.length === 0) {
        this.onlyOneChild = { ... parent, path: '', noShowingChildren: true }
        return true
      }

      return false
    },
    resolvePath(routePath) {
      if (isExternal(routePath)) {
        return routePath
      }
      if (isExternal(this.basePath)) {
        return this.basePath
      }
      return path.resolve(this.basePath, routePath)
    }
  }
}
</script>
```





项目实践

src/main.js

```js
import Vue from 'vue'

import 'normalize.css/normalize.css' // A modern alternative to CSS resets

import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
// import locale from 'element-ui/lib/locale/lang/en' // lang i18n

import '@/styles/index.scss' // global css

import App from './App'
import store from './store'
import router from './router'

import '@/icons' // icon
import '@/permission' // permission control
import 'mavon-editor/dist/css/index.css' // 文档样式
// 二维码
import QROcode from 'vue-qriously'
// 全局组件
import MyCom from './components/index'
// 全局工具类
import Utils from './utils/utils'
// 引入时间过滤器
import * as filters from './utils/filters'
// 注册为全局过滤器
Object.keys(filters).forEach(key => {
  Vue.filter(key, filters[key])
})
/**
 * This project originally used easy-mock to simulate data,
 * but its official service is very unstable,
 * and you can build your own service if you need it.
 * So here I use Mock.js for local emulation,
 * it will intercept your request, so you won't see the request in the network.
 * If you remove `../mock` it will automatically request easy-mock data.
 */
// import '../mock' // simulation data

// Vue.use(ElementUI, { locale })
Vue.use(ElementUI)
Vue.use(MyCom)
Vue.use(QROcode)
// 阻止 vue 在启动时生成生产提示。
Vue.config.productionTip = false
Vue.prototype.$Utils = Utils
new Vue({
  el: '#app',
  router,
  store,
  render: h => h(App)
})
```

src/permission.js

```js
import router from './router'
import store from './store'
import { Message, MessageBox } from 'element-ui'
import NProgress from 'nprogress'
import 'nprogress/nprogress.css'
import { getToken } from '@/utils/auth'
import { getSystemVersion } from './utils/checkVersion'
// import Cookies from 'js-cookie'
NProgress.configure({ showSpinner: false })
// 不重定向白名单
const whiteList = ['/login', '/testlogin', '/signature/index', '/sketchpad/index', '/signature/qrcode_callback']

router.beforeEach(async(to, from, next) => {
  NProgress.start()
  if (getToken()) {
    if (to.path === '/login') {
      next({ path: '/' })
      NProgress.done()
    } else {
      // 判断当前用户是否已拉取完user_info信息
      const hasRoles = store.getters.roles && store.getters.roles.length > 0
      if (!hasRoles) {
        try {
          // 获取品牌配置
          await store.dispatch('getBrandConfigDetail')
          // 获取用户角色及权限
          const roles = await store.dispatch('getSysUserInfo')
          // 根据roles权限生成可访问的路由表
          const addRoutes = await store.dispatch('GenerateRoutes', { roles })
          // 动态添加可访问路由表
          router.addRoutes(addRoutes)
          // hack方法 确保addRoutes已完成
          next({ ...to, replace: true })
        } catch (e) {
          await store.dispatch('FedLogOut')
          Message.error(e || '验证失败，请重新登录')
          next({ path: '/' })
        }
      } else {
        try {
          const versionStatus = await getSystemVersion()
          if (versionStatus) {
            MessageBox.alert('发现系统有更新，刷新一下可加速哦~', '提示', {
              confirmButtonText: '好的',
              callback: action => { location.reload() }
            })
          }
          next()
        } catch (e) {
          next()
        }
      }
    }
  } else {
    await store.dispatch('getBrandConfigDetail')
    // 在免登录白名单，直接进入
    if (whiteList.indexOf(to.path) !== -1) {
      next()
    } else {
      // 否则全部重定向到登录页
      // next(`/login?redirect=${to.path}`)
      next(`/login?redirect=/`)
      NProgress.done()
    }
  }
})
// 结束Progress
router.afterEach(() => {
  NProgress.done()
})
```

src/store/index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'
import app from './modules/app'
import user from './modules/user'
// import permission from './modules/permission'
import permission from './modules/permission.dev'
import tagsView from './modules/tagsView'
import getters from './getters'
import brandConfig from './modules/brand_config'
Vue.use(Vuex)

const store = new Vuex.Store({
  modules: {
    app,
    user,
    permission,
    tagsView,
    brandConfig
  },
  getters
})

export default store
```

src/store/modules/permission.js

```js
import { constantRouterMap } from '@/router'
import { getSystemSideBarList } from '@/service/rbac'
import lazyLoading from '@/router/lazyLoading'
import Layout from '@/views/layout/Layout'
/**
 * 通过meta.role判断是否与当前用户权限匹配
 * @param roles
 * @param route
 */
function hasPermission(roles, route) {
  if (route.meta && route.meta.roles) {
    return roles.some(role => route.meta.roles.includes(role))
  } else {
    return true
  }
}

/**
 * 递归过滤异步路由表，返回符合用户角色权限的路由表
 * @param routes asyncRouterMap
 * @param roles
 */
function filterAsyncRouter(routes, roles) {
  const res = []

  routes.forEach(route => {
    const tmp = { ...route }
    if (hasPermission(roles, tmp)) {
      if (tmp.children) {
        tmp.children = filterAsyncRouter(tmp.children, roles)
      }
      res.push(tmp)
    }
  })

  return res
}

/**
 * 递归动态路由数据清洗
 * @param routers
 * @param data
 * @returns {*}
 */
function initSideBarMenu(routers, data) {
  for (let item of data) {
    let menu = {}
    menu.path = item.url
    menu.name = item.router_name
    // 处理组件路径
    if (menu.component === 'Layout') {
      menu.component = Layout
    } else {
      menu.component = lazyLoading(item.component)
    }
    menu.hidden = !!item.is_hidden
    menu.alwaysShow = !!item.always_show
    menu.redirect = item.redirect
    // 处理是否有二级菜单
    if (item.child && item.child.length > 0) {
      menu.children = []
      // 将二级菜单中的权限追加到父级菜单中
      item.permission = item.child.map(v => v.permission)
      initSideBarMenu(menu.children, item.child)
    } else {
      //  将字符串权限格式化为数组
      item.permission = item.permission ? item.permission.split(',') : []
    }
    let meta = {}
    // 给页面添加权限、标题、等参数
    if (!item.is_white) {
      meta.roles = item.permission
    }
    meta.title = item.name ? item.name : '未知'
    meta.icon = item.ico
    meta.noCache = !!item.no_cache
    meta.affix = !!item.affix
    meta.visited = !!item.visited
    meta.breadcrumb = !!item.breadcrumb
    menu.meta = meta

    routers.push(menu)
  }
  // console.log('asyncMenuData==', routers)
  return routers
}

const permission = {
  state: {
    routes: constantRouterMap,
    addRouters: []
  },
  mutations: {
    SET_ROUTERS: (state, routers) => {
      state.addRouters = routers
      state.routes = constantRouterMap.concat(routers)
    }
  },
  actions: {
    // 获取动态侧边栏菜单
    async getSystemSideBarList() {
      try {
        const res = await getSystemSideBarList({}, { loading: false }) // 数据清洗
        const constRoutes = []
        return initSideBarMenu(constRoutes, res.data)
      } catch (e) {
        //
      }
    },
    async GenerateRoutes({ commit, dispatch }, data) {
      const siderBarMenu = await dispatch('getSystemSideBarList')
      const { roles } = data
      return new Promise(resolve => {
        let accessedRouters = filterAsyncRouter(siderBarMenu, roles)
        // console.log('accessedRouters==', JSON.parse(JSON.stringify(accessedRouters)))
        // 判断是否有首页路由、否则就拿第一个路由作为默认路由
        // 原默认路由是指向首页、如果动态菜单没有首页路由，那么一登录就会404
        const dashboard_routers = accessedRouters.filter(v => v.name === 'dashboard')
        // console.log('has_dashboard', dashboard_routers)
        if (!dashboard_routers.length) {
          // 默认拿第一个菜单，第一个路由作为默认路由 避免一直访问404
          const redirect = `${accessedRouters[0].path}/${accessedRouters[0].children[0].path}`
          const redirect_path = { path: '/', redirect, hidden: true }
          accessedRouters = accessedRouters.concat(redirect_path)
        }
        // console.log('dashboard_routers==', accessedRouters)
        commit('SET_ROUTERS', accessedRouters)
        resolve(accessedRouters)
      })
    }
  }
}

export default permission
```

src/store/modules/permission.dev.js

```js
import { asyncRouterMap, constantRouterMap } from '@/router'
/**
 * 通过meta.role判断是否与当前用户权限匹配
 * @param roles
 * @param route
 */
function hasPermission(roles, route) {
  if (route.meta && route.meta.roles) {
    return roles.some(role => route.meta.roles.includes(role))
  } else {
    return true
  }
}

/**
 * 递归过滤异步路由表，返回符合用户角色权限的路由表
 * @param routes asyncRouterMap
 * @param roles
 */
function filterAsyncRouter(routes, roles) {
  const res = []

  routes.forEach(route => {
    const tmp = { ...route }
    if (hasPermission(roles, tmp)) {
      if (tmp.children) {
        tmp.children = filterAsyncRouter(tmp.children, roles)
      }
      res.push(tmp)
    }
  })

  return res
}

const permission = {
  state: {
    routes: constantRouterMap,
    addRouters: []
  },
  mutations: {
    SET_ROUTERS: (state, routers) => {
      state.addRouters = routers
      state.routes = constantRouterMap.concat(routers)
    }
  },
  actions: {
    GenerateRoutes({ commit }, data) {
      return new Promise(resolve => {
        const { roles } = data
        let accessedRouters = filterAsyncRouter(asyncRouterMap, roles)
        commit('SET_ROUTERS', accessedRouters)
        resolve(accessedRouters)
      })
    }
  }
}

export default permission
```

src/router/index.js

```js
import Vue from 'vue'
import Router from 'vue-router'
// 开发环境中不使用延迟加载

Vue.use(Router)

/* Layout */
import Layout from '../views/layout/Layout'

/**
* hidden: true                   是否显示侧边栏
* alwaysShow: true               始终显示根菜单
* redirect: noredirect           将不会在面包屑中重定向
* name:'router-name'             该名称由<keep-alive>使用（必须设置!!!）
* meta : {
    title: 'title'               subMenu和breadcrumb中显示的名称（推荐设置）
    icon: 'svg-name'             侧边栏中的图标显示
    breadcrumb: false            是否显示在面包屑中
    affix:false                  是否固定在标签导航中默认为false
    noCache: true                是否取消缓存，默认为false
    visited: false               是否显示在标签导航中，默认为false
  }
**/
// 代表那些不需要动态判断权限的路由，如登录页、404、等通用页面。
// ['shopowner', 'reception', 'area_manager', ' sub_manager']
//   店长，前台，区域经理，子区域经理
export const constantRouterMap = [
  { path: '/login', component: () => import('@/views/login/Login'), hidden: true },
  { path: '/testlogin', component: () => import('@/views/login/TestLogin'), hidden: true },
  { path: '/404', component: () => import('@/views/404'), hidden: true },
  // 刷新标签页时做重定向时使用
  {
    path: '/redirect',
    component: Layout,
    hidden: true,
    children: [
      {
        path: '/redirect/:path*',
        component: () => import('@/views/redirect/index')
      }
    ]
  },
  // 电子单详情
  {
    path: '/signature/print',
    name: 'SignaturesPrint',
    component: () => import('@/views/card/SignaturesPrint'),
    hidden: true,
    meta: { title: '电子单签名详情' }
  },
  // 还款 电子单详情
  {
    path: '/repay_signature/print',
    name: 'RepaySignaturesPrint',
    component: () => import('@/views/card/order/detail/RepaySignaturesPrint'),
    hidden: true,
    meta: { title: '电子单签名详情' }
  },
  // 出库电子单打印
  {
    path: '/outreserve/print',
    name: 'OutReservePrint',
    component: () => import('@/views/setting/invoicing/OutReservePrint'),
    hidden: true,
    meta: { title: '出库电子单' }
  },
  // 扫码电子签名
  {
    path: '/signature/index',
    name: 'Signature',
    component: () => import('@/views/UserSignature'),
    hidden: true,
    meta: { title: '电子签名' }
  },
  // 签名画板
  {
    path: '/sketchpad/index',
    name: 'Sketchpad',
    component: () => import('@/views/SketchpadCanvas'),
    hidden: true,
    meta: { title: '电子签名板' }
  },
  // 消费订单-小票打印
  {
    path: '/receipt/order_print',
    name: 'OrderReceiptPrint',
    component: () => import('@/views/card/order/OrderReceiptPrint'),
    hidden: true,
    meta: { title: '小票打印' }
  },
  // 充值订单-小票打印
  {
    path: '/receipt/recharge_print',
    name: 'RechargeReceiptPrint',
    component: () => import('@/views/card/recharge/RechargeReceiptPrint'),
    hidden: true,
    meta: { title: '小票打印' }
  }
]
// 代表那些需求动态判断权限并通过 addRouters 动态添加的页面。
export const asyncRouterMap = [
  {
    path: '/',
    component: Layout,
    name: 'dashboard',
    redirect: '/dashboard',
    meta: { roles: ['entry_clerk', 'shopowner', 'reception', 'area_manager', 'sub_manager', 'service'] },
    children: [{
      path: 'dashboard',
      name: 'Dashboard',
      component: () => import('@/views/dashboard/index'),
      meta: {
        title: '首页',
        roles: ['entry_clerk', 'shopowner', 'reception', 'area_manager', 'sub_manager', 'service'],
        icon: 'index',
        noCache: true,
        affix: true
      }
    }]
  },
  // rabc 用户权限管理
  {
    path: '/rbac',
    component: Layout,
    name: 'rbac',
    alwaysShow: true,
    meta: {
      title: '用户权限管理',
      icon: 'customer',
      roles: ['entry_clerk', 'area_manager', 'sub_manager']
    },
    children: [
      {
        path: 'permission',
        component: () => import('@/views/rbac/Permission'),
        name: 'RbacPermission',
        meta: {
          title: '权限管理',
          roles: ['entry_clerk'],
          noCache: true
        }
      },
      {
        path: 'group',
        component: () => import('@/views/rbac/group/GroupIndex'),
        name: 'RbacGroupIndex',
        meta: {
          title: '授权组管理',
          noCache: true
        }
      },
      {
        path: 'batch_permission',
        component: () => import('@/views/rbac/batch_permission/Index'),
        name: 'BatchPermissionIndex',
        meta: {
          title: '权限分配',
          noCache: true
        }
      },
      {
        path: 'assign_permission',
        component: () => import('@/views/rbac/assign_permission/Index'),
        name: 'AssignPermissionIndex',
        hidden: true,
        meta: {
          title: '可阅读权限组',
          noCache: true
        }
      }
    ]
  },
  // 经营统计
  {
    path: '/operate',
    component: Layout,
    name: 'operate',
    alwaysShow: true,
    meta: {
      roles: ['entry_clerk', 'shopowner', 'reception', 'area_manager', 'sub_manager'],
      title: '经营统计',
      icon: 'statistics'
    },
    children: [
      {
        path: 'dashboard',
        component: () => import('@/views/operate/Index'),
        name: 'OperateIndex',
        meta: {
          title: '统计报表'
        }
      },
      {
        path: 'referral_performance',
        component: () => import('@/views/operate/referral _performance_statement/ReferralPerformanceStatement'),
        name: 'ReferralPerformance',
        meta: {
          title: '转诊业绩报表'
        }
      },
      {
        path: 'shop_owner',
        component: () => import('@/views/operate/shop_owner/OpShopOwnerList'),
        name: 'OpShopOwnerList',
        meta: {
          noCache: true,
          title: '店长业绩报表'
        }
      }
    ]
  },
  // 顾客管理
  {
    path: '/customers',
    component: Layout,
    name: 'customers',
    alwaysShow: true,
    redirect: '/customers/customerList',
    meta: {
      title: '顾客管理',
      icon: 'customer'
    },
    children: [
      {
        path: 'customerList',
        component: () => import('@/views/customers/customersList/customerList'),
        name: 'CustomersList',
        meta: {
          title: '顾客列表',
          affix: true,
          noCache: true
        }
      },
      {
        path: 'customerDetails/:id',
        component: () => import('@/views/customers/customersList/customerDetails'),
        name: 'customerDetails',
        hidden: true,
        meta: {
          title: '顾客详情'
        }
      }
    ]
  },
  // 运营统计
  {
    path: '/operation',
    component: Layout,
    name: 'Operation',
    alwaysShow: true,
    redirect: '/operation/dashboard',
    meta: {
      title: '运营统计',
      icon: 'statistics',
      roles: ['entry_clerk']
    },
    children: [
      {
        path: 'dashboard',
        component: () => import('@/views/operation/Index'),
        name: 'OperationIndex',
        meta: {
          title: '活跃统计'
        }
      },
      {
        path: 'activeStore/index/:status/:date/:datesTwo',
        component: () => import('@/views/operation/template/store/activeStore'),
        name: 'ActiveStore',
        hidden: true,
        meta: {
          title: '活跃门店'
        }
      },
      {
        path: 'monitoring_statistics',
        component: () => import('@/views/operation/monitoring_statistics/Index'),
        name: 'MonitoringStatistics',
        meta: {
          title: '监控统计'
        }
      }
    ]
  },
  // 店务管理
  {
    path: '/card',
    component: Layout,
    name: 'card',
    alwaysShow: true,
    redirect: '/card/recharege_list',
    meta: {
      title: '店务管理',
      icon: 'matters',
      roles: ['entry_clerk', 'shopowner', 'reception', 'area_manager', 'sub_manager']
    },
    children: [
      {
        path: 'recharege_list',
        component: () => import('@/views/card/recharge/RecharegeList'),
        name: 'RecharegeList',
        meta: {
          title: '充值记录',
          noCache: true
        }
      },
      {
        path: 'recharege_add/:t',
        hidden: true,
        component: () => import('@/views/card/recharge/RecharegeAdd'),
        name: 'RecharegeAdd',
        meta: {
          title: '新建充值'
        }
      }
    ]
  },
  // 预约管理
  {
    path: '/reserve',
    component: Layout,
    name: 'reserve',
    alwaysShow: true,
    meta: {
      title: '预约管理',
      icon: 'reserve',
      roles: ['entry_clerk', 'shopowner', 'reception', 'service', 'area_manager', 'sub_manager']
    },
    children: [
      {
        path: 'reserve_first',
        component: () => import('@/views/reserve/ReserveFirst'),
        name: 'ReserveFirst',
        meta: {
          title: '网络预约',
          noCache: true
        }
      },
      {
        path: 'index',
        component: () => import('@/views/reserve/Index'),
        name: 'ReserveIndex',
        meta: {
          roles: ['entry_clerk', 'shopowner', 'reception', 'service', 'area_manager', 'sub_manager'],
          title: '门店自约',
          noCache: true
        }
      }
    ]
  },
  // 员工管理
  {
    path: '/manager',
    component: Layout,
    name: 'manager',
    meta: {
      title: '员工管理',
      icon: 'manager',
      roles: ['entry_clerk', 'reception', 'shopowner', 'area_manager', 'sub_manager']
    },
    children: [
      {
        path: 'personnel_list',
        component: () => import('@/views/manager/personnel/PersonnelList'),
        name: 'PersonnelList',
        meta: {
          title: '员工列表',
          noCache: true
        }
      },
      {
        path: 'account_number_list',
        component: () => import('@/views/manager/account_number/Index'),
        name: 'ApprovalAccountNumber',
        meta: {
          title: '待办审批'
        }
      }
    ]
  },
  // 账号信息
  {
    path: '/usersetting',
    component: Layout,
    hidden: true,
    name: 'usersetting',
    meta: {
      title: '账号设置'
    },
    children: [
      {
        path: 'index',
        component: () => import('@/views/usersetting/index'),
        name: 'UserIndex',
        meta: {
          title: '账号设置'
        }
      }
    ]
  },
  // 应用中心
  {
    path: '/setting',
    component: Layout,
    name: 'setting',
    meta: {
      roles: ['reception', 'entry_clerk', 'shopowner', 'area_manager', 'sub_manager']
    },
    children: [
      {
        path: 'index',
        component: () => import('@/views/setting/index'),
        name: 'SettingIndex',
        meta: {
          icon: 'app_center',
          title: '应用中心'
        }
      },
      {
        path: 'card',
        component: () => import('@/views/setting/card_manage/Index'),
        name: 'CardManageIndex',
        hidden: true,
        meta: {
          noCache: true,
          title: '卡项设置'
        }
      }
    ]
  },
  // 支付平台
  {
    path: '/payment_plat',
    component: Layout,
    hidden: true,
    name: 'PaymentPlat',
    redirect: '/payment_plat/index',
    meta: {
      title: '支付平台',
      icon: 'customer'
    },
    children: [
      {
        path: 'index',
        component: () => import('@/views/setting/payment_plat/Index'),
        name: 'PaymentPlatIndex',
        meta: {
          roles: ['entry_clerk', 'shopowner', 'reception', 'area_manager', 'sub_manager'],
          title: '在线收银台',
          noCache: true
        }
      },
      {
        path: 'payment_plat_list',
        component: () => import('@/views/setting/payment_plat/payment_plat_list/Index'),
        name: 'PaymentPlatList',
        meta: {
          roles: ['entry_clerk', 'shopowner', 'reception', 'area_manager', 'sub_manager'],
          title: '支付平台列表',
          noCache: true
        }
      },
      {
        path: 'payment_plat_apply',
        component: () => import('@/views/setting/payment_plat/payment_apply/Index'),
        name: 'PaymentPlatApply',
        meta: {
          roles: ['entry_clerk', 'shopowner', 'reception', 'area_manager', 'sub_manager'],
          title: '支付平台审核',
          noCache: true
        }
      }
    ]
  },
  // 系统公告
  {
    path: '/notice',
    component: Layout,
    name: 'Notice',
    hidden: true,
    children: [
      {
        path: 'index',
        component: () => import('@/views/notice/Index'),
        name: 'NoticeList',
        meta: {
          title: '公告列表',
          noCache: true
        }
      }
    ]
  },
  // 配置
  {
    path: '/sysconfig',
    component: Layout,
    name: 'sysconfig',
    meta: {
      roles: ['entry_clerk', 'shopowner', 'reception', 'area_manager', 'sub_manager']
    },
    children: [
      {
        path: 'index',
        component: () => import('@/views/sysconfig/Index'),
        name: 'SysConfig',
        meta: {
          title: '系统配置',
          icon: 'setting'
        }
      }
    ]
  },
  // 联系我们
  {
    path: '/aboutme',
    component: Layout,
    name: 'aboutme',
    children: [
      {
        path: 'index',
        component: () => import('@/views/aboutme/index'),
        name: 'AboutMe',
        meta: {
          icon: 'aboutme',
          title: '联系我们'
        }
      }
    ]
  },
  // 系统管理
  {
    path: '/system',
    component: Layout,
    name: 'SystemManagement',
    meta: {
      icon: 'aboutme',
      title: '系统管理',
      roles: ['entry_clerk']
    },
    children: [
      {
        path: 'behavior_manage',
        component: () => import('@/views/system-management/behavior_manage/Index'),
        name: 'BehaviorManage',
        meta: {
          title: '行为管理'
        }
      },
      {
        path: 'system_sidebar_menu',
        component: () => import('@/views/system-management/sidebar/Index'),
        name: 'SystemSideBarMenu',
        meta: {
          title: '菜单管理'
        }
      },
      {
        path: 'log_manage',
        component: () => import('@/views/system-management/log_manage/Index'),
        name: 'SystemLogIndex',
        meta: {
          title: '日志管理'
        }
      }
    ]
  },
  {
    path: '/lottery/chongqing',
    component: () => import('@/views/lottery/Index'),
    name: 'ChongQingLottery',
    hidden: true,
    meta: {
      title: '重庆活动抽奖'
    }
  },
  {
    path: '/document/:id',
    component: () => import('@/views/document/Index'),
    name: 'Document',
    hidden: true
  },
  { path: '/document', redirect: '/document/1', hidden: true },
  { path: '*', redirect: '/404', hidden: true }
]

export default new Router({
  // mode: 'history', // 后端支持可开
  scrollBehavior: () => ({ y: 0 }),
  routes: constantRouterMap
})
```



















































