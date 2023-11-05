## Node 接口

### server.js

```js
// 引入 express
const express = require('express')
const helmet = require('helmet')
// 创建 express 实例
const app = express()
// 引入 mongoose
const mongoose = require('mongoose')
// 引入 body-parser中间件 获得 post 请求响应内容
const bodyParser = require('body-parser')
// 引入 passport中间件
const passport = require('passport')

const session = require('express-session')
const MongoStore = require('connect-mongo')(session)

// 引入 users 路由中间件
const users = require('./routes/api/users')
// 引入 profiles 路由中间件
const profiles = require('./routes/api/profiles')

// 数据库地址
const db = require('./config/key').mongoURI
// 连接数据库
mongoose
  .set('useUnifiedTopology', true)
  .connect(db, { useNewUrlParser: true, useFindAndModify: false })
  .then(() => console.log('已连接数据库'))
  .catch((err) => console.error('无法连接数据库', err))

// 设置跨域和相应数据格式
app.all('/api/*', function (req, res, next) {
  res.header('Access-Control-Allow-Origin', '*')
  res.header(
    'Access-Control-Allow-Headers',
    'Content-Type, Authorization, X-Requested-With'
  )
  res.header('Access-Control-Allow-Methods', 'PUT,POST,GET,DELETE,OPTIONS')
  res.header('Access-Control-Allow-Credentials', true) //可以带cookies
  res.header('X-Powered-By', 'Express')
  if (req.method === 'OPTIONS') res.sendStatus(200)
  /*让 options 请求快速返回*/ else next()
})

app.use(helmet())

app.use(
  session({
    name: 'email',
    secret: 'sendEmailCode', // 用来对 session id相关的 cookie进行签名
    store: new MongoStore({
      url: db,
      touchAfter: 24 * 3600, // 不管发出多少请求，24小时内只更新一次 session，除非你改变 session
    }), // 数据库存储session
    saveUninitialized: false, // 强制将未初始化的 session 存储。当新建了一个 session 且未设定属性或值时，它就处于
    //未初始化状态。在设定一个 cookie 前，这对于登录验证，减轻服务端存储压力，权限控制是有帮助的。这里建议 false
    resave: false, // 是否每次都强制保存session
    cookie: {
      secure: false, // 只有https协议才设置为true
      httpOnly: true, // 禁止客户端JavaScript的访问，不能使用document.cookie
      maxAge: 10 * 60 * 1000, // 有效期，单位是毫秒，这里设为 10 分钟 10 * 60 * 1000
    },
    // rolling: true // 每次请求都强制对 session 续期
  })
)

// 使用 body-parser ，处理程序之前，在中间件中对 post请求的请求体进行解析
// 使用 body-parser 中间件 必须写在路由中间件前面（问题解决）
// 解析我们通常的 form 表单提交的数据，也就是请求头中包含这样的信息：
// Content-Type 为 application/x-www-form-urlencoded ，为 false 表示使用系统模块 querystring 来解析成对象
app.use(bodyParser.urlencoded({ extended: false }))
// 请求 body 解析成 application/json ，解析 json 数据格式
app.use(bodyParser.json())
// 使用 passport 中间件，初始化 passport
app.use(passport.initialize())
// 头像上传
app.use('/tmp_uploads', express.static('tmp_uploads'))
// 使用 passport 中间件验证 token
require('./config/passport')(passport)

// 测试路由
// app.get("/", (req, res) => {
//   res.send("Hello World!")
// })

// 使用 users 路由中间件
app.use('/api/users', users)
app.use('/api/profiles', profiles)
// 生产环境使用 port 作为端口，开发环境使用 5001 作为端口
// package.json 在生产环境使用 npm run start，在开发环境使用 npm run server
const port = process.env.port || 5001

// 监听端口
app.listen(port, () => {
  console.log(`Server running on port ${port}`)
})
```

### config/key.js

```js
// 不同环境使用不同数据库
module.exports = {
  mongoURI: "mongodb://localhost/backend", // backend 数据库
  secretKey: "secret",
}
```

### config/passport.js

```js
// passport 是一个针对密码验证中间件，无缝衔接基于 Express的 web 服务
// passport-jwt 是一个针对 jsonwebtoken 的插件，验证 token 通过后才能进行正常响应
const JwtStrategy = require("passport-jwt").Strategy,
ExtractJwt = require("passport-jwt").ExtractJwt;
const User = require("../models/User");
const keys = require("./key");

const opts = {};
opts.jwtFromRequest = ExtractJwt.fromAuthHeaderAsBearerToken(); // 从验证头中提取，模型默认是'Bearer '.
opts.secretOrKey = keys.secretKey;

module.exports = passport => {
  // passport.js 有策略(strategy)设计模式的概念. strategy 是一些预定义的方法,
  // 策略会在请求抵达真正的路由之前执行.
  // 如果定义的 strategy（针对 jwt）认定某个请求非法, 则该路由不会被执行, 而是返回 401 Unauthorized.
  // 第一个参数 opts 包含用于控制如何从请求中提取 token 的对象或者被验证的选项的对象
  // 第二个参数是具有参数 (jwt_payload, done)的 verify函数
  // 第三个参数是 callback, done 是 Passport 错误，第一个回调接受参数( 错误，用户，信息)
  passport.use(
    new JwtStrategy(opts, async function (jwt_payload, done) {
      try {
        // jwt_payload 返回的是登录时返回的数据 即 payload
        const user = await User.findById(jwt_payload.id)
        if (user) {
          // 此处会将 user 信息写入 req.user
          return done(null, user)
        } else {
          return done(null, false)
        }
      }
      catch (ex) {
        console.log(ex);
      }
    })
  )
}

models/Users.js

// 引入 mongoose
const mongoose = require("mongoose");
// 引入 mongoose 的骨架
const Schema = mongoose.Schema;

// 创建用户骨架 接口数据的字段与类型 用户所有信息的数据库字段
const userSchema = new Schema({
  name: {
    type: String,
    required: true,
  },
  email: {
    type: String,
    required: true,
  },
  password: {
    type: String,
    required: true,
  },
  avatar: {
    type: String,
  },
  identity: {
    type: String,
    required: true,
  },
  date: {
    type: String,
    default: Date.now,
  },
})

// 使用 mongoose 创建用户模型
const User = mongoose.model("user", userSchema);
// 导出用户模型
module.exports = User
```

### models/Profile.js

```js
// 引入 mongoose
const mongoose = require("mongoose")
// 引入 mongoose 的骨架
const Schema = mongoose.Schema

// 创建资金流水骨架 接口数据的字段与类型
const profileSchema = new Schema({
  type: {
    type: String,
  },
  desc: {
    type: String,
  },
  income: {
    type: String,
    required: true,
  },
  expenses: {
    type: String,
    required: true,
  },
  cash: {
    type: String,
    required: true,
  },
  remark: {
    type: String,
  },
  date: {
    type: Date,
    default: Date.now,
  },
})

// 使用 mongoose 创建资金流水模型
const Profile = mongoose.model("profile", profileSchema)
// 导出资金流水模型
module.exports = Profile
```

### routes/users.js

```js
// 引入 express
const express = require('express')
// 创建路由实例
const router = express.Router()
// 引入 User 模型
const User = require('../../models/User')
// 引入 bcrypt
const bcrypt = require('bcrypt')
// 引入全球公认头像
const gravatar = require('gravatar')
// 引入 jwt
const jwt = require('jsonwebtoken')
// 引入原始 key
const keys = require('../../config/key')
// 引入 passport中间件
const passport = require('passport')
// 引入图片验证码
const svgCaptcha = require('svg-captcha')
// 引入发送验证码
const { registerEmail, codeEmail } = require('./code')


const fs = require('fs')
const path = require('path')
// multer是 express 官方推荐的文件上传中间件（另外还有 formidable），用于处理 multipart/form-data 类型的表单数据
// 基于 busboy 开发
const multer = require('multer')
// 先自定义头像上传的路径
const upload = multer({ dest: 'tmp_uploads/' })


// 创建测试路由
// 公共接口  GET api/users/test
// 返回 json 数据
// router.get("/test", (req, res) => {
//   res.json({msg: "Test"})
// })


// 发送注册邮箱验证码
router.get('/getRegisterCode', (req, res) => {
  registerEmail(req, res)
})
// 创建注册路由
// 公共接口  Post api/users/register
// 返回 json 数据
router.post('/register', async (req, res) => {
  // console.log(req.body)
  const verifyCode = req.body.verifyCode // 邮箱验证码
  if (verifyCode === '') {
    return res.json({ error: true, message: '验证码不能为空!' })
  }
  // 查询数据库中是否已有该邮箱
  const user = await User.findOne({ email: req.body.email })
  // 接收返回的文档对象 user 后，继续处理
  if (user) {
    return res.status(400).send('邮箱已注册!')
  }
  if (req.session.registerCode === verifyCode) {
    // 存放默认头像图片
    const avatar = gravatar.url(req.body.email, {
      s: '200',
      r: 'g',
      d: 'mm',
    })
    const nowDate = String(new Date().getTime())
    // 把表单的请求数据结合模型存进数据库
    const newUser = new User({
      name: req.body.name,
      email: req.body.email,
      avatar,
      identity: req.body.identity,
      password: req.body.password,
      date: nowDate,
    })
    // 加盐，对 newUser 密码进行哈希加密后返回 newUser 的 JSON 数据
    bcrypt.genSalt(10, function (err, salt) {
      bcrypt.hash(newUser.password, salt, function (err, hash) {
        if (err) {
          res.json({ error: true, message: '系统错误!' })
        }
        newUser.password = hash
        newUser
          .save()
          .then((user) =>
            res.json({ user, success: true, message: '注册用户成功' })
          )
          .catch((err) => console.log(err))
      })
    })
    // 验证通过，删除保存的验证码
    delete req.session.registerCode
  } else {
    if (!req.session.registerCode) {
      res.json({
        error: true,
        message: '验证码已过期，请重新获取!',
      })
    } else {
      res.json({
        error: true,
        message: '验证码错误!',
      })
    }
  }
})


// 获取图片验证码
// 公共接口  Get api/users/getCaptcha
// 返回 cookie 和 验证码字符串
router.get('/getCaptcha', (req, res) => {
  var captcha = svgCaptcha.create({
    // 字体大小
    fontSize: 38,
    // 验证码去掉模糊人眼容易认错的字符
    ignoreChars: '0o1iLlI',
    // 噪声线条数
    noise: 3,
    // 宽度
    width: 80,
    // 高度
    height: 32,
  })
  // 保存到 session,忽略大小写
  req.session.captcha = captcha.text.toLowerCase()
  // console.log(req.session) // 打印生成的验证码，如 0xtg
  //保存到 cookie 方便前端调用验证
  res.type('svg')
  res.send(captcha.data)
  res.end()
})
// 创建登录路由
// 公共接口  POST api/users/login
// 返回 json 数据
// 先查询数据库中该邮箱是否存在，再匹配密码是否一致，最后才登录成功
router.post('/login', async (req, res) => {
  const email = req.body.email
  const password = req.body.password
  const captcha = req.body.inputCaptcha.toLowerCase()
  if (captcha !== req.session.captcha) {
    return res.json({ error: true, message: '验证码错误!' })
  }
  // 验证通过，删除保存的验证码
  delete req.session.captcha
  const user = await User.findOne({ email })
  if (!user) {
    return res.json({ error: true, message: '邮箱或密码错误!' })
  }
  const isMatch = await bcrypt.compare(password, user.password)
  // 如果登录密码一致
  if (isMatch) {
    const rule = {
      id: user.id,
      name: user.name,
      identity: user.identity,
      avatar: user.avatar,
    }
    // 数字签名生成 token
    // 四个参数：认证依据,私有key, 过期时间, 生成 token 后的回调
    jwt.sign(rule, keys.secretKey, { expiresIn: 3600 }, function (err, token) {
      // 生成并返回 token
      res.json({
        success: true,
        // 使用 passport时，token前面一定要有 Bearer 持有人
        token: 'Bearer ' + token,
      })
    })
    // res.json({msg: "success"})
  } else {
    return res.json({ error: true, message: '邮箱或密码错误!' })
  }
})


// 发送重置邮箱验证码
router.get('/getEmailCode', (req, res) => {
  codeEmail(req, res)
})
// 重置密码
router.put('/resetPass', async (req, res) => {
  const email = req.body.email
  const password = req.body.password2 //密码
  const verifyCode = req.body.verifyCode // 邮箱验证码
  // console.log(req.session.emailCode)
  if (verifyCode === '') {
    return res.json({ error: true, message: '验证码不能为空!' })
  }
  // 查询该账号是否存在
  const user = await User.findOne({ email: email })
  if (!user) {
    return
  } else {
    // 判断验证码是否正确、验证码时间是否超过10分钟
    if (req.session.emailCode === verifyCode) {
      // 加盐，对 newUser 密码进行哈希加密后返回 newUser 的 JSON 数据
      bcrypt.genSalt(10, function (err, salt) {
        bcrypt.hash(password, salt, async (err, hash) => {
          if (err) {
            res.json({ error: true, message: '系统错误!' })
          }
          // 更新数据库的用户信息，把用户名密码也存进去
          let nowDate = String(new Date().getTime()) //获取当前时间
          const newUser = await User.findByIdAndUpdate(
            user._id,
            { password: hash, date: nowDate },
            { new: true }
          ) // new: true返回修改后的数据
          if (newUser) {
            res.status(200).json({
              success: true,
              message: '修改密码成功,请登录',
            })
            // 验证通过，删除保存的验证码
            delete req.session.emailCode
          }
        })
      })
    } else {
      if (!req.session.emailCode) {
        res.json({
          error: true,
          message: '验证码已过期，请重新获取!',
        })
      } else {
        res.json({
          error: true,
          message: '验证码错误!',
        })
      }
    }
  }
})


// 创建 jwt 认证后生成当前用户的路由
// 私有接口  GET api/users/login
// 返回 当前 user
// 认证请求很简单，只需要调用 passport.authenticate() 中间件并且指定要应用的策略
// 成功认证之后，Passport 将建立一个持续的登录会话，请求提供凭证时不必要
// 使用 jwt 就不再需要 session 保存用户数据了。
router.get(
  '/current',
  passport.authenticate('jwt', { session: false }),
  (req, res) => {
    res.json({
      id: req.user.name,
      name: req.user.name,
      email: req.user.email,
      identity: req.user.identity,
    })
  }
)


// 创建上传头像的路由 单图上传
// 路由中间件 upload.single() 接受一个 name 以 fieldname 命名的文件，如 <input type="file" name="file">
router.post('/updateAvatar/:id', upload.single('file'), async function (
  req,
  res,
  next
) {
  // multer 会添加一个 body 对象 以及 file 或 files 对象 到 express 的 req 对象中。
  // body 对象包含表单的文本域信息，file 或 files 对象包含对象表单上传的文件信息。
  // file.originalname 用户上传的原始文件名
  var fileExtArray = req.file.originalname.split('.') // ['e2b65d86bba4a9ca951767463a1b50b1', 'jpg']
  // ext 上传文件的后缀
  var ext = fileExtArray[fileExtArray.length - 1]
  var targetPath = req.file.path + '.' + ext
  // multer 会自动生成新的上传文件名
  // req.file.path 要上传文件的路径：tmp_uploads\e2b65d86bba4a9ca951767463a1b50b1
  // targetPath ：tmp_uploads\e2b65d86bba4a9ca951767463a1b50b1.jpg
  // 将头像存进数据库
  await User.findOneAndUpdate(
    { _id: req.params.id },
    { $set: { avatar: 'http://47.115.15.21:5001/' + targetPath } }
  )
  // 上传的文件由于不包含文件后缀，无法预览，所以需要重命名，req.file.path 变为 targetPath
  // 异步地把 oldPath 文件重命名为 newPath 提供的路径名。如果 newPath 已存在，则覆盖它。
  // 将头像存进服务器
  fs.rename(path.join(req.file.path), path.join(targetPath), function (err) {
    if (err) {
      return res.json('上传文件失败!')
    }
    res.status(200).json({
      tmp_path: targetPath,
      url: 'http://47.115.15.21:5001/' + targetPath,
    })
  })
})
// 导出路由中间件
module.exports = router
```

### routes/profiles.js

```js
// 引入 express
const express = require('express')
// 创建路由实例
const router = express.Router()
// 引入 Profile 模型
const Profile = require('../../models/Profile')
// 引入 passport中间件
const passport = require('passport')

// 查询获取所有资金
// 私有接口  Get api/profiles
// 返回 json 数据
router.get(
  '/',
  passport.authenticate('jwt', { session: false }),
  async (req, res) => {
    try {
      const profiles = await Profile.find()
      if (!profiles) {
        return res.status(404).json('没有任何内容')
      }
      res.json(profiles)
    } catch (ex) {
      console.log(res.status(404).json(ex))
    }
  }
)

// 查询搜索单个资金 根据 id
// 私有接口  Get api/profiles/:id
// 返回 json 数据
router.get(
  '/:id',
  passport.authenticate('jwt', { session: false }),
  async (req, res) => {
    try {
      const profiles = await Profile.find({ _id: req.params.id })
      if (!profiles) {
        return res.status(404).json('没有任何内容')
      }
      res.json(profiles)
    } catch (ex) {
      console.log(res.status(404).json(ex))
    }
  }
)

// 创建测试路由
// 公共接口  GET api/profiles/test
// 返回 json 数据
// router.get("/test", (req, res) => {
//   res.json({msg: "Test"})
// })

// 创建添加资金路由
// 私有接口  Post api/profiles/add
// 返回 json 数据
router.post(
  '/add',
  passport.authenticate('jwt', { session: false }),
  async (req, res) => {
    const profileFields = {}
    if (req.body.type) profileFields.type = req.body.type
    if (req.body.desc) profileFields.desc = req.body.desc
    if (req.body.income) profileFields.income = req.body.income
    if (req.body.expenses) profileFields.expenses = req.body.expenses
    if (req.body.cash) profileFields.cash = req.body.cash
    if (req.body.remark) profileFields.remark = req.body.remark
    const profile = await new Profile(profileFields).save()
    res.json(profile)
  }
)

// 编辑资金
// 私有接口  Post api/profiles/edit
// 返回 json 数据
router.post(
  '/edit/:id',
  passport.authenticate('jwt', { session: false }),
  async (req, res) => {
    const profileFields = {}
    if (req.body.type) profileFields.type = req.body.type
    if (req.body.desc) profileFields.desc = req.body.desc
    if (req.body.income) profileFields.income = req.body.income
    if (req.body.expenses) profileFields.expenses = req.body.expenses
    if (req.body.cash) profileFields.cash = req.body.cash
    if (req.body.remark) profileFields.remark = req.body.remark
    const profile = await Profile.findOneAndUpdate(
      { _id: req.params.id },
      { $set: profileFields },
      { new: true }
    )
    res.json(profile)
  }
)

// 删除一条资金
// 私有接口  Delete api/profiles/delete/:id
// 返回 json 数据
router.delete(
  '/delete/:id',
  passport.authenticate('jwt', { session: false }),
  async (req, res) => {
    try {
      // 问题 删除之后不需要 save
      const profile = await Profile.findByIdAndRemove({ _id: req.params.id })
      res.json(profile)
    } catch (ex) {
      console.log(res.status(404).json('删除失败'))
    }
  }
)

// 批量删除多条资金
// 私有接口  Delete api/profiles/multiDelete
// 返回 json 数据
router.post(
  '/multiDelete',
  passport.authenticate('jwt', { session: false }),
  async (req, res) => {
    let arr = req.body
    const profile = await Profile.deleteMany({ _id: { $in: arr } }, (err) => {
      if (err) {
        console.log(res.status(404).json('删除失败'))
      }
    })
    res.json(profile)
  }
)

// 导出路由中间件
module.exports = router
```

### routes/code.js

```js
// 引入 url模块解析 url 字符串
const url = require('url')
// 引入 querystring 模块处理 query 字符串
const querystring = require('querystring')
// 文件读写
const fs = require('fs')
// 路径配置
const path = require('path')
// 引入 nodemailer
const nodemailer = require('nodemailer')
// ejs模版引擎
const ejs = require('ejs')

// 创建一个 SMTP 客户端配置
const config = {
  host: 'smtp.qq.com',
  port: 465,
  secure: true, // 使用SSL方式（安全方式，防止被窃取信息）
  auth: {
    user: '2067659951@qq.com', // 邮箱账号
    pass: 'hannozbcpopfchch', // smtp 授权码，到邮箱设置下获取，不是注册时的密码
  },
}

// 创建一个 SMTP 客户端对象
const transporter = nodemailer.createTransport(config)

// 发送注册邮件
function registerEmail(req, res) {
  let emailNum = Math.random().toString().slice(-6) // 随机数转字符串截取后6位，生成6位随机数验证码
  // 获取返回的 url对象的 query 属性值，如： /getEmailCode?email=1570038342%40qq.com
  let arg = url.parse(req.url).query
  // 将arg参数字符串反序列化为一个对象（字符串转对象）
  let params = querystring.parse(arg) // {email: '1570038342@qq.com'}
  let email = params.email //邮箱
  req.session.registerCode = emailNum //把验证码存储进session
  //传给EJS的数据
  let data = {
    verification: emailNum,
  }
  let template = ejs.compile(
    fs.readFileSync(
      path.resolve(__dirname, '../../views/registerEmail.ejs'),
      'utf8'
    )
  )
  let html = template(data)
  let mailOptions = {
    from: '2067659951@qq.com', // 发送者
    to: email, // 接受者,可以同时发送多个,以逗号隔开
    subject: '注册验证码', // 标题
    html: html, //也可以用html发送
  }
  transporter.sendMail(mailOptions, function (error, info) {
    let succ = {}
    if (error) {
      succ.succ = false
      succ.msg = '发送失败'
    } else {
      succ.succ = true
      succ.msg = '发送成功'
    }
    res.json(succ)
  })
}


// 发送重置密码邮件
function codeEmail(req, res) {
  // console.log(req)
  let emailNum = Math.random().toString().slice(-6) // 随机数转字符串截取后6位，生成6位随机数验证码
  // 获取返回的 url对象的 query 属性值，如： /getEmailCode?email=1570038342%40qq.com
  let arg = url.parse(req.url).query
  // 将 arg 参数字符串 email=1570038342%40qq.com 反序列化为一个对象，查询字符串中的百分比编码字符使用UTF-8编码
  let params = querystring.parse(arg) // {email: '1570038342@qq.com'}
  let email = params.email //邮箱
  // console.log(email)
  req.session.emailCode = emailNum //把验证码存储进session
  // 传给EJS的验证码数据
  let data = {
    verification: emailNum,
  }
  let template = ejs.compile(
    fs.readFileSync(path.resolve(__dirname, '../../views/email.ejs'), 'utf8')
  )
  // 使用验证码数据生成 html 模板
  let html = template(data)
  let mailOptions = {
    from: '2067659951@qq.com', // 发送者
    to: email, // 接受者,可以同时发送多个,以逗号隔开
    subject: '重置账户密码', // 标题
    // text: '邮箱验证码有效期为10分钟,验证码:' + req.session.emailCode,
    html: html, //也可以用html发送
  }
  transporter.sendMail(mailOptions, function (error, info) {
    let succ = {}
    if (error) {
      succ.succ = false
      succ.msg = '发送失败'
    } else {
      succ.succ = true
      succ.msg = '发送成功'
    }
    res.json(succ)
  })
}

module.exports = {
  registerEmail,
  codeEmail,
}
```

## client 前端

### APP.vue

```vue
<template>
  <div id="app">
    <!-- 路由出口 -->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <!-- 渲染 / 下的所有子路由组件 -->
    <router-view/>
  </div>
</template>

<script>
import jwt_decode from "jwt-decode";

export default {
  name: 'app',
  created() {
    // 这里可以设置自动登录
    if (localStorage.eletoken) {
      const decode = jwt_decode(localStorage.eletoken);
      this.$store.dispatch("setIsAuthenticated", !this.isEmpty(decode));
      this.$store.dispatch("setUser", decode);
    }
  },
  methods: {
    isEmpty(value) {
      return (
        value === undefined ||
        value === null ||
        (typeof value === "object" && Object.keys(value).length === 0) ||
        (typeof value === "string" && value.trim().length === 0)
      );
    }
  }
}
</script>

<style>
html,
body,
#app {
  width: 100%;
  height: 100%;
}
</style>
```

### main.js

```js
// 引入 vue
import Vue from 'vue'
import {
  Pagination,
  Dialog,
  Select,
  Submenu,
  Menu,
  MenuItem,
  Dropdown,
  DropdownMenu,
  DropdownItem,
  Input,
  Option,
  Button,
  Table,
  TableColumn,
  DatePicker,
  Form,
  FormItem,
  Tabs,
  TabPane,
  Icon,
  Row,
  Col,
  Upload,
  Progress,
  Card,
  Loading,
  MessageBox,
  Message,
  Checkbox,
  Notification,
} from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import App from './App.vue'
// 引入 Vue Router
import router from './router'
// 引入 Vuex
import store from './store'
// 引入 axios
import axios from './http'


Vue.config.productionTip = false
Vue.use(Pagination)
Vue.use(Dialog)
Vue.use(Menu)
Vue.use(Submenu)
Vue.use(MenuItem)
Vue.use(Dropdown)
Vue.use(DropdownMenu)
Vue.use(DropdownItem)
Vue.use(Input)
Vue.use(Select)
Vue.use(Option)
Vue.use(Button)
Vue.use(Table)
Vue.use(TableColumn)
Vue.use(DatePicker)
Vue.use(Form)
Vue.use(FormItem)
Vue.use(Tabs)
Vue.use(TabPane)
Vue.use(Icon)
Vue.use(Row)
Vue.use(Col)
Vue.use(Upload)
Vue.use(Progress)
Vue.use(Card)
Vue.use(Checkbox)
Vue.use(Loading.directive)

Vue.prototype.$loading = Loading.service
Vue.prototype.$msgbox = MessageBox
Vue.prototype.$confirm = MessageBox.confirm
Vue.prototype.$message = Message
Vue.prototype.$notify = Notification

Vue.prototype.$axios = axios

new Vue({
  router,
  store,
  render: (h) => h(App),
}).$mount('#app')
```

### http.js

```js
const axios = require("axios")
import { Message, Loading } from 'element-ui';
import router from './router/index'

let loading;
// 加载动画
function startLoading() {
  loading = Loading.service({
    lock: true,
    text: "加载中",
    background: "rgba(0, 0, 0, 0.75)"
  })
}
// 结束加载动画
function endLoading() {
  loading.close();
}

axios.defaults.baseURL = 'http://47.115.15.21:5001/api';
axios.defaults.timeout = 5000;
// cookie-session，请求需要凭证
axios.defaults.withCredentials = true

// 添加请求拦截器
axios.interceptors.request.use(config => {
  // 在发送请求之前做些什么
  // 请求开始时加载动画开始
  startLoading();
  // localStorage.eleToken 存在时设置统一的请求头 授权
  if (localStorage.eleToken) {
    config.headers.Authorization = localStorage.eleToken
  }
  return config;
}, err => {
  // 对请求错误做些什么
  return Promise.reject(err)
})

// 添加响应拦截器
axios.interceptors.response.use(
  // 对响应数据做点什么
  response => {
    // 响应有结果了, 结束 loading 动画
    endLoading()
    return response
  },
  error => {
    // 对响应数据做点什么
    // 结束 loading
    endLoading()
    // 查看状态码是不是 401 ,如果是则表示 token 已过期
    // 获取状态码
    const {status} = error.response
    if (status === 401) {
      // 错误提醒
      Message.error("登录已过期，请重新登录")
      // 清除 token
      localStorage.removeItem('eleToken');
      // 跳转到登录页
      router.push('/login');
    }
    return Promise.reject(error)
  }
)

export default axios;
```

vue.config.js

```js
const CompressionPlugin = require('compression-webpack-plugin');

module.exports = {
  publicPath: '/release/',
  outputDir: 'release',
  productionSourceMap: false,
  filenameHashing: false,
  chainWebpack: (config) => {
    config.entry.app = ['babel-polyfill', './src/main.js']
    // 移除 prefetch 插件
    config.plugins.delete('prefetch')
  },
  configureWebpack: (config) => {
    config.externals = {
      echarts: 'echarts',
    },
      // 生产环境删除console
    config.optimization.minimizer[0].options.terserOptions.compress = {
      drop_console: true,
      drop_debugger: false,
      pure_funcs: ['console.log'],
    } // 移除console
    config.optimization.splitChunks.cacheGroups = {
      vendor: {
        chunks: 'all',
        test: /[\\/]node_modules[\\/]/,
        name: 'vendor',
        minChunks: 1,
        maxInitialRequests: 5,
        minSize: 0,
        priority: 100,
      },
      common: {
        chunks: 'all',
        test: /[\\/]src[\\/]js[\\/]/,
        name: 'common',
        minChunks: 2,
        maxInitialRequests: 5,
        minSize: 0,
        priority: 60,
      },
      styles: {
        name: 'styles',
        test: /\.(le|sa|sc|c)ss$/,
        chunks: 'all',
        reuseExistingChunk: true,
        minChunks: 1,
        enforce: true,
      },
    },
      //gzip压缩
      config.plugins.push(
        new CompressionPlugin({
          //压缩算法
          algorithm: 'gzip',
          //匹配文件
          test: /\.js$|\.css$|\.html$|\.woff$|\.ttf$|\.eot$|/,
          //压缩超过此大小的文件,以字节为单位
          threshold: 1024,
          minRatio: 0.8,
          //删除原始文件只保留压缩后的文件
          deleteOriginalAssets: false,
        })
      )
  },
  css: {
    loaderOptions: {
      postcss: {
        plugins: [require('autoprefixer')({})],
      },
    },
  },
  devServer: {
    open: true,
    host: 'localhost',
    port: 8080,
    https: false,
    hotOnly: false,
    // proxy: {
    //   // 配置跨域
    //   '/api': {
    //     target: 'http://47.115.15.21:5001/api/',
    //     ws: true,
    //     changeOrigin: true,
    //     pathRewrite: {
    //       '^/api': '', // target 地址替代 axios 请求接口的 /api
    //     },
    //   },
    // },
    before: (app) => {},
  },
}
```

### babel.config.js

```js
module.exports = {
  presets: [
    '@vue/app'
  ]
}
```

### router/index.js

```js
// 引入 Vue
import Vue from 'vue'
// 引入 Vue 路由
import VueRouter from 'vue-router'
import { Message } from 'element-ui'

// 使用 Vue 路由
Vue.use(VueRouter)

// 引入组件并懒加载
const Register = () => import(/* webpackChunkName: "chunk-all" */ '../views/Register')
const Login = () => import(/* webpackChunkName: "chunk-all" */ '../views/Login')
const ForgotPassword = () => import(/* webpackChunkName: "chunk-all" */ '../views/ForgotPassword')
const NotFound = () => import(/* webpackChunkName: "chunk-all" */ '../views/404')
const Index = () => import(/* webpackChunkName: "chunk-all" */ '../views/Index')
const Home = () => import(/* webpackChunkName: "chunk-all" */ '../views/Home')
const Info = () => import(/* webpackChunkName: "chunk-all" */ '../views/Info')
const FundList = () => import(/* webpackChunkName: "chunk-all" */ '../views/FundList')
const Tabs = () => import(/* webpackChunkName: "chunk-all" */ '../views/Tabs')

// 创建 routes 路由与子路由组成的数组（各个路径对应的组件与子组件）
const routes = [
  {
    path: '/',
    redirect: '/login', // 如果路径为 / 重定向为登录页
  },
  {
    path: '/index',
    component: Index,
    children: [
      {
        path: '',
        component: Home, // /index 默认为首页
      },
      {
        path: '/home',
        name: 'home',
        component: Home,
      },
      {
        path: '/fundlist',
        name: 'fundlist',
        component: FundList,
      },
      {
        path: '/info',
        name: 'info',
        component: Info,
      },
      {
        path: '/tabs',
        name: 'tabs',
        component: Tabs,
      },
    ],
  },
  {
    path: '/login',
    name: 'login',
    component: Login,
  },
  {
    path: '/register',
    name: 'register',
    component: Register,
  },
  {
    path: '/forgot',
    name: 'forgot',
    component: ForgotPassword,
  },
  {
    path: '*',
    name: '404',
    component: NotFound, // 其他路径则为 404
  },
]

const router = new VueRouter({
  mode: 'history',
  base: '/release/',
  routes,
})

// 路由守卫 没登录之前只能访问注册页和登录页
router.beforeEach((to, from, next) => {
  // 获取 token ，有 token 代表已登录
  const isLogin = localStorage.eleToken
  // 如果已注册或已登录则正常访问所有页面
  if (
    to.path === '/register' ||
    to.path === '/login' ||
    to.path === '/forgot'
  ) {
    // next() 正常访问
    next()
  }
  // 否则想进入其他页面只能跳转到登录页
  else {
    isLogin ? next() : Message.error('请先登录') && next('/login')
  }
})

// 路由到登录界面时，强制页面reload
// router.afterEach((to,from)=>{
//     if(from.path != '/login' && from.path != '/' && to.path == '/login'){
//         window.location.reload();
//     }
// })
export default router
```

### store/index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'
import createPersistedState from "vuex-persistedstate"

Vue.use(Vuex)

// 设置 type 有利于调试
const types = {
  SET_AUTHENTICATED: "SET_AUTHENTICATED",
  SET_USER: "SET_USER",
}

const state = {
  isAuthenticated: false,
  user: {},
  rememberPwd: '',
  
}

const getters = {
  isAuthenticated: state => state.isAuthenticated,
  user: state => state.user
}

const actions = {
  setIsAuthenticated: ({ commit }, isAuthenticated) => {
    commit(types.SET_AUTHENTICATED, isAuthenticated)
  },
  setUser: ({ commit }, user) => {
    commit(types.SET_USER, user)
  },
  clearState: ({commit}) => {
    commit(types.SET_AUTHENTICATED, false);
    commit(types.SET_USER, null);
  }
}

const mutations = {
  [types.SET_AUTHENTICATED](state, isAuthenticated) {
     if (isAuthenticated) {
       state.isAuthenticated = isAuthenticated
     }
     else {
       state.isAuthenticated = false
     }
  },
  [types.SET_USER](state, user) {
    if (user) {
      state.user = user
    }
    else {
      state.user = {}
    }
  }
}

export default new Vuex.Store({
  state,
  getters,
  mutations,
  actions,
  // 缓存 Vuex 数据，保证 F5刷新时 Vuex 数据不被清理
  plugins: [createPersistedState({ storage: window.sessionStorage })],
})
```

### views/404.vue

```vue
<template>
  <div class="wscn-http404-container">
    <div class="wscn-http404">
      <div class="pic-404">
        <img class="pic-404__parent" src="../assets/404.png" alt="404">
        <img class="pic-404__child left" src="../assets/404_cloud.png" alt="404">
        <img class="pic-404__child mid" src="../assets/404_cloud.png" alt="404">
        <img class="pic-404__child right" src="../assets/404_cloud.png" alt="404">
      </div>
      <div class="bullshit">
        <div class="bullshit__oops">OOPS!</div>
        <div class="bullshit__info">All rights reserved
          <a style="color:#20a0ff" href="https://wallstreetcn.com" target="_blank">wallstreetcn</a>
        </div>
        <div class="bullshit__headline">{{ message }}</div>
        <div class="bullshit__info">Please check that the URL you entered is correct, or click the button below to return to the homepage.</div>
        <a href="/release" style="text-decoration:none;" class="bullshit__return-home">Back to home</a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Notfound',
  computed: {
    message() {
      return 'The webmaster said that you can not enter this page...'
    }
  }
}
</script>

<style lang="scss" scoped>
.wscn-http404-container{
  transform: translate(-50%,-50%);
  position: absolute;
  top: 40%;
  left: 50%;
}
.wscn-http404 {
  position: relative;
  width: 1200px;
  padding: 0 50px;
  overflow: hidden;
  .pic-404 {
    position: relative;
    float: left;
    width: 600px;
    overflow: hidden;
    &__parent {
      width: 100%;
    }
    &__child {
      position: absolute;
      &.left {
        width: 80px;
        top: 17px;
        left: 220px;
        opacity: 0;
        animation-name: cloudLeft;
        animation-duration: 2s;
        animation-timing-function: linear;
        animation-fill-mode: forwards;
        animation-delay: 1s;
      }
      &.mid {
        width: 46px;
        top: 10px;
        left: 420px;
        opacity: 0;
        animation-name: cloudMid;
        animation-duration: 2s;
        animation-timing-function: linear;
        animation-fill-mode: forwards;
        animation-delay: 1.2s;
      }
      &.right {
        width: 62px;
        top: 100px;
        left: 500px;
        opacity: 0;
        animation-name: cloudRight;
        animation-duration: 2s;
        animation-timing-function: linear;
        animation-fill-mode: forwards;
        animation-delay: 1s;
      }
      @keyframes cloudLeft {
        0% {
          top: 17px;
          left: 220px;
          opacity: 0;
        }
        20% {
          top: 33px;
          left: 188px;
          opacity: 1;
        }
        80% {
          top: 81px;
          left: 92px;
          opacity: 1;
        }
        100% {
          top: 97px;
          left: 60px;
          opacity: 0;
        }
      }
      @keyframes cloudMid {
        0% {
          top: 10px;
          left: 420px;
          opacity: 0;
        }
        20% {
          top: 40px;
          left: 360px;
          opacity: 1;
        }
        70% {
          top: 130px;
          left: 180px;
          opacity: 1;
        }
        100% {
          top: 160px;
          left: 120px;
          opacity: 0;
        }
      }
      @keyframes cloudRight {
        0% {
          top: 100px;
          left: 500px;
          opacity: 0;
        }
        20% {
          top: 120px;
          left: 460px;
          opacity: 1;
        }
        80% {
          top: 180px;
          left: 340px;
          opacity: 1;
        }
        100% {
          top: 200px;
          left: 300px;
          opacity: 0;
        }
      }
    }
  }
  .bullshit {
    position: relative;
    float: left;
    width: 300px;
    padding: 30px 0;
    overflow: hidden;
    &__oops {
      font-size: 32px;
      font-weight: bold;
      line-height: 40px;
      color: #1482f0;
      opacity: 0;
      margin-bottom: 20px;
      animation-name: slideUp;
      animation-duration: 0.5s;
      animation-fill-mode: forwards;
    }
    &__headline {
      font-size: 20px;
      line-height: 24px;
      color: #222;
      font-weight: bold;
      opacity: 0;
      margin-bottom: 10px;
      animation-name: slideUp;
      animation-duration: 0.5s;
      animation-delay: 0.1s;
      animation-fill-mode: forwards;
    }
    &__info {
      font-size: 13px;
      line-height: 21px;
      color: grey;
      opacity: 0;
      margin-bottom: 30px;
      animation-name: slideUp;
      animation-duration: 0.5s;
      animation-delay: 0.2s;
      animation-fill-mode: forwards;
    }
    &__return-home {
      display: block;
      float: left;
      width: 110px;
      height: 36px;
      background: #1482f0;
      border-radius: 100px;
      text-align: center;
      color: #ffffff;
      opacity: 0;
      font-size: 14px;
      line-height: 36px;
      cursor: pointer;
      animation-name: slideUp;
      animation-duration: 0.5s;
      animation-delay: 0.3s;
      animation-fill-mode: forwards;
    }
    @keyframes slideUp {
      0% {
        transform: translateY(60px);
        opacity: 0;
      }
      100% {
        transform: translateY(0);
        opacity: 1;
      }
    }
  }
}
</style>
```

### views/Register.vue

```vue
<template>
  <div class="register">
    <section class="form_title">
      <div class="manage_title">
        <span class="title">影片资金后台管理系统</span>
        <!--
          主要修改 :model rules ref v-model prop 添加 placeholder 与 html 代码无异
          :model el-form 表单数据与 data 的 regisstreUser（整个表单数据，一个对象）双向绑定
          rules 为表单 data 数据的校验规则
          ref 标记 el-form 表单，用于在提交时判断用户输入验证是否有效 this.$refs[formName].validate
          label-width 设置每个 el-form-item 的宽度
          -->
        <el-form
          :model="registerUser"
          :rules="rules"
          ref="registerForm"
          label-width="80px"
          class="registerForm"
          hide-required-asterisk
        >
          <!--
            prop 为 data 的数据，el-form 组件传值给子组件 el-form-item
            每个 el-input 都使用 v-model 与 registerUser 其中的一个属性数据绑定
            -->
          <el-form-item label="用户名" prop="name">
            <el-input
              v-model="registerUser.name"
              prefix-icon="el-icon-user"
              placeholder="请输入用户名"
            >
            </el-input>
          </el-form-item>
          <el-form-item label="邮箱" prop="email">
            <el-input
              type="email"
              v-model="registerUser.email"
              prefix-icon="el-icon-message"
              placeholder="请输入邮箱"
            >
            </el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input
              :type="pwdType"
              v-model="registerUser.password"
              prefix-icon="el-icon-lock"
              placeholder="请输入密码"
            >
              <i
                slot="suffix"
                title="显示或隐藏密码"
                :class="showPwd"
                :style="{ color: '#409eff', cursor: 'pointer' }"
                @click="togglePwd"
              >
              </i>
            </el-input>
          </el-form-item>
          <el-form-item label="确认密码" prop="password2">
            <el-input
              :type="pwdType"
              v-model="registerUser.password2"
              prefix-icon="el-icon-lock"
              placeholder="请确认密码"
            >
              <i
                slot="suffix"
                title="显示或隐藏密码"
                :class="showPwd"
                :style="{ color: '#409eff', cursor: 'pointer' }"
                @click="togglePwd"
              >
              </i>
            </el-input>
          </el-form-item>
          <el-form-item label="选择身份">
            <el-select
              v-model="registerUser.identity"
              placeholder="请选择身份"
              class="select-identity"
            >
              <el-option label="管理员" value="manager"></el-option>
              <el-option label="员工" value="employee"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="验证码" prop="verifyCode">
            <div class="vertify">
              <el-input
                prefix-icon="el-icon-key"
                v-model="registerUser.verifyCode"
                placeholder="验证码"
                auto-complete="off"
              ></el-input>
              <!--
                btnMsg 使用 data 数据插值，创建一个 time 初始为 30 秒，
                setInterval --time ，按钮的 disabled 为 true, btnMsg 显示秒数
                当 time < 0 ,,按钮的 disabled 为 false, btnMsg 变回原来的数据
                -->
              <el-button
                type="primary"
                plain
                @click="getCode('registerForm')"
                :disabled="isDisabled"
                class="emailcode"
              >
                {{ btnMsg }}
              </el-button>
            </div>
          </el-form-item>
          <el-form-item>
            <el-button
              type="primary"
              class="submit_btn"
              @click="submitForm('registerForm')"
            >
              注册
            </el-button>
          </el-form-item>
          <div class="tip_area">
            <p><router-link to="/login">已有账号？马上登录</router-link></p>
          </div>
        </el-form>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'register',
  data() {
    const validatePass2 = (rule, value, callback) => {
      if (value !== this.registerUser.password) {
        callback(new Error('两次输入密码不一致'))
      } else {
        callback()
      }
    }
    return {
      pwdType: 'password',
      isShowPwd: false,
      btnMsg: '获取邮箱验证码',
      isDisabled: false,
      time: 30,
      registerUser: {
        name: '',
        email: '',
        password: '',
        password2: '',
        identity: '',
        verifyCode: '',
      },
      rules: {
        name: [
          { required: true, message: '用户名不能为空', trigger: 'blur' },
          {
            min: 2,
            max: 8,
            message: '用户名长度必须在2到8之间',
            trigger: 'blur',
          },
        ],
        email: [
          { required: true, message: '邮箱不能为空', trigger: 'blur' },
          { type: 'email', message: '请输入正确的邮箱地址', trigger: 'blur' },
        ],
        password: [
          { required: true, message: '密码不能为空', trigger: 'blur' },
          {
            min: 6,
            max: 10,
            message: '密码长度必须在6到10之间',
            trigger: 'blur',
          },
        ],
        password2: [
          { required: true, message: '确认密码不能为空', trigger: 'blur' },
          {
            min: 6,
            max: 10,
            message: '确认密码长度必须在6到10之间',
            trigger: 'blur',
          },
          { validator: validatePass2, trigger: 'blur' },
        ],
        identity: [
          { required: true, message: '请选择身份', trigger: 'change' },
        ],
      },
    }
  },
  computed: {
    showPwd() {
      return this.isShowPwd ? 'third-icon-alieye' : 'third-icon-alino_eye'
    },
  },
  methods: {
    togglePwd() {
      this.pwdType === 'password'
        ? (this.pwdType = '')
        : (this.pwdType = 'password')
      this.isShowPwd = !this.isShowPwd
    },
    // 验证码发送
    getCode(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // axios 的 get 请求使用 params 作为查询字符串，专门针对 GET 请求参数的拼接
          this.$axios
            .get('/users/getRegisterCode', {
              params: {
                email: this.registerUser.email,
              },
            })
            .then((res) => {
              if (res.data.succ) {
                this.$message({
                  message: '邮件已发送，请查收',
                  type: 'success',
                })
                this.isDisabled = true
                let interval = setInterval(() => {
                  this.btnMsg = '' + this.time + '秒'
                  --this.time
                  if (this.time < 0) {
                    this.btnMsg = '获取邮箱验证码'
                    this.time = 30
                    this.isDisabled = false
                    clearInterval(interval)
                  }
                }, 1000)
              }
            })
            .catch((err) => {
              this.$message({
                message: '系统错误!',
                type: 'error',
              })
              //  console.log('Error', err)
            })
        }
      })
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        // post 只需要在 url 后添加第二个参数为 data
        this.$axios
          .post('/users/register', this.registerUser)
          .then((res) => {
            if (res.data.success) {
              this.$message({
                message: '账号注册成功',
                type: 'success',
              })
              this.$router.push('/login')
            } else {
              this.$message({
                message: res.data.message,
                type: 'error',
              })
            }
          })
          .catch((err) => {
            this.$message({
              message: '邮箱已注册!',
              type: 'error',
            })
            this.$router.push('/register')
          })
      })
    },
  },
}
</script>

<style scoped>
.register {
  position: relative;
  width: 100%;
  height: 100%;
  background: url(../assets/login-bg.jpg) no-repeat center center;
  background-size: 100% 100%;
}
.form_title {
  width: 370px;
  height: 210px;
  position: absolute;
  top: 5%;
  left: 34%;
  padding: 25px;
  border-radius: 5px;
  text-align: center;
}
.form_title .title {
  font-family: 'Microsoft YaHei';
  font-weight: bold;
  font-size: 26px;
  color: #111;
}
.registerForm {
  margin-top: 20px;
  background-color: #fff;
  padding: 20px 40px 20px 20px;
  border-radius: 5px;
  box-shadow: 0px 5px 10px #cccc;
}
.vertify {
  display: flex;
  justify-content: space-between;
}
.vertify >>> .emailcode {
  margin-left: 10px;
  font-size: 10px;
}
.tip_area {
  text-align: left;
  font-size: 12px;
  color: #333;
}
.tip_area a {
  color: #409eff;
}
.submit_btn {
  width: 100%;
}
</style>
<style>
.select-identity input {
  padding-left: 30px;
}
</style>
```

### views/Login.vue

```vue
<template>
  <div class="login">
    <section class="form_container">
      <div class="manage_tip">
        <span class="title">影片资金后台管理系统</span>
      </div>
      <el-form
        label-position="top"
        :model="loginUser"
        :rules="rules"
        ref="loginForm"
        class="loginForm"
        label-width="60px"
        hide-required-asterisk
      >
        <div class="login_title">
          <h3>账号密码登录</h3>
        </div>
        <el-form-item label="邮箱" prop="email" style="margin-bottom: 10px;">
          <el-input
            v-model="loginUser.email"
            prefix-icon="el-icon-message"
            placeholder="邮箱"
          ></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password" style="margin-bottom: 10px;">
          <el-input
            v-model="loginUser.password"
            prefix-icon="el-icon-lock"
            placeholder="密码"
            :type="pwdType"
          >
            <i
              slot="suffix"
              title="显示或隐藏密码"
              :class="showPwd"
              :style="{ color: '#409eff', cursor: 'pointer' }"
              @click="togglePwd"
            >
            </i>
          </el-input>
        </el-form-item>
        <el-form-item
          label="验证码"
          prop="inputCaptcha"
          style="margin-bottom: 21px;"
        >
          <div class="verification">
            <el-input
              prefix-icon="el-icon-key"
              v-model="loginUser.inputCaptcha"
              placeholder="验证码"
            ></el-input>
            <img
              width="80"
              style="background:#EEE9E9;margin-left:30px;cursor:pointer;"
              ref="captcha"
              height="40"
              src="http://47.115.15.21:5001/api/users/getCaptcha"
              @click="refreshCaptcha"
            />
          </div>
        </el-form-item>
        <div class="remember_pwd">
          <el-checkbox v-model="checked">记住密码</el-checkbox>
        </div>
        <div class="login_btn">
          <el-button
            type="primary"
            @click="submitForm('loginForm')"
            class="submit_btn"
            >登 录
          </el-button>
        </div>
        <div class="tip_area">
          <p><router-link to="/forgot">忘记密码</router-link></p>
          <p><router-link to="/register">还没有账号？现在注册</router-link></p>
        </div>
      </el-form>
    </section>
  </div>
</template>


<script>
import bus from '../components/bus'
import jwt_decode from 'jwt-decode'
import AES from 'crypto-js/aes'
import ENC from 'crypto-js/enc-utf8'   
export default {
  name: 'login',
  data() {
    const validateCaptcha = (rule, value, callback) => {
      if (!this.loginUser.inputCaptcha.trim().length) {
        callback(new Error('请输入验证码'))
      } else {
        callback()
      }
    }
    return {
      loginUser: {
        email: '123452@qq.com',
        password: '123452',
        inputCaptcha: '',
      },
      pwdType: 'password',
      isShowPwd: false,
      checked: false,
      reminder: null,
      rules: {
        email: [
          { required: true, message: '邮箱不能为空', trigger: 'blur' },
          { type: 'email', message: '请输入正确的邮箱地址', trigger: 'blur' },
        ],
        password: [
          { required: true, message: '密码不能为空', trigger: 'blur' },
          {
            min: 6,
            max: 10,
            message: '密码长度为 6 到 10 个字符',
            trigger: 'blur',
          },
        ],
        inputCaptcha: [
          { required: true, validator: validateCaptcha, trigger: 'blur' },
        ],
      },
    }
  },
  computed: {
    showPwd() {
      return this.isShowPwd ? 'third-icon-alieye' : 'third-icon-alino_eye'
    },
  },
  created() {
    bus.$on('userLogout', this.loadAccountInfo())
  },
  mounted() {
    // console.log('mounted调用了')
    this.showTip()
    bus.$on('refreshCaptcha', this.refreshCaptcha())
  },
  methods: {
    togglePwd() {
      this.pwdType === 'password'
        ? (this.pwdType = '')
        : (this.pwdType = 'password')
      this.isShowPwd = !this.isShowPwd
    },
    showTip() {
      const h = this.$createElement
      this.reminder = this.$notify({
        title: '提示',
        message: h(
          'i',
          { style: 'color: teal' },
          '管理员账号和员工账号分别为：123452@qq.com 和 123453@qq.com，密码为邮箱账号名，注册和忘记密码需真实邮箱'
        ),
        duration: 6000,
        iconClass: 'el-icon-s-opportunity',
      })
      // console.log('提示调用了')
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // console.log(this.loginUser.inputCaptcha)
          // console.log(this.getCookie("captcha"))
          // 验证 验证码与邮箱、密码
          this.$axios
            .post('/users/login', this.loginUser)
            .then((res) => {
              if (res.data.error) {
                this.$message({
                  message: res.data.message,
                  type: 'error',
                })
                this.refreshCaptcha()
              }
              // 登录成功
              // 获取响应的 token
              const { token } = res.data
              // 将 token 存放到 localStorage
              localStorage.setItem('eleToken', token)
              // 使用 jwt_decode 解析token
              const decode = jwt_decode(token)
              // token 存储到 Vuex 中
              // decode 不为空时才授权，将 Vuex 的 state 的 isAuthenticated 设为 true
              this.$store.dispatch('setIsAuthenticated', !this.isEmpty(decode))
              // 将 token 存储到 Vuex 的 state 的 user 里
              this.$store.dispatch('setUser', decode)
              this.$message.success('登录成功')
              // 页面跳转
              this.$router.push('/index')
              if (this.reminder) {
                this.reminder.close()
              }
              this.reminder = null
            })
            .catch((err) => {
              console.log('Error', err)
            })
          //定义要存入cookie的对象
          let accountInfo = ''
          // 拿到输入框中的密码，使用AES加密
          let pwd = this.loginUser.password
          let newPwd = AES.encrypt(pwd, 'secret key 123')
          // 若勾选记住密码
          if (this.checked === true) {
            // console.log('选择记住密码，checked == true')
            accountInfo = this.loginUser.email + '&' + newPwd //将加密后的密码存入cookie对象中
            this.setUserCookie('accountInfo', accountInfo, 1440 * 7) //传入账号名，密码，和保存天数3个参数（7天）
          } else {
            // console.log('清空Cookie')
            this.clearUserCookie('accountInfo') // 清空Cookie
          }
        }
      })
    },
    // 工具函数，判断 token 是否为空，如果为空则为 true
    isEmpty(value) {
      return (
        value === undefined ||
        value === null ||
        (typeof value === 'object' && Object.keys(value).length === 0) ||
        (typeof value === 'string' && value.trim().length === 0)
      )
    },
    // 刷新验证码
    refreshCaptcha() {
      this.loginUser.inputCaptcha = ''
      this.$refs.captcha.src =
        'http://47.115.15.21:5001/api/users/getCaptcha?d=' + Math.random()
    },
    // 获取验证码cookie
    getCookie(cname) {
      let name = cname + '='
      let ca = document.cookie.split(';')
      for (let i = 0; i < ca.length; i++) {
        let c = ca[i].trim()
        if (c.indexOf(name) === 0) return c.substring(name.length, c.length)
      }
      return ''
    },
    setUserCookie(cookieName, value, expiremMinutes) {
      var exdate = new Date()
      exdate.setTime(exdate.getTime() + expiremMinutes * 60 * 1000)
      document.cookie =
        cookieName +
        '=' +
        escape(value) +
        (expiremMinutes == null ? '' : ';expires=' + exdate.toGMTString())
    },
    getUserCookie(cookieName) {
      if (document.cookie.length > 0) {
        var c_start = document.cookie.indexOf(cookieName + '=')
        if (c_start != -1) {
          c_start = c_start + cookieName.length + 1
          var c_end = document.cookie.indexOf(';', c_start)
          if (c_end == -1) c_end = document.cookie.length
          return unescape(document.cookie.substring(c_start, c_end))
        }
      }
      return ''
    },
    clearUserCookie(cookieName) {
      var exp = new Date()
      exp.setTime(exp.getTime() - 1)
      var cval = this.getCookie(cookieName)
      if (cval != null) {
        document.cookie =
          cookieName + '=' + cval + ';expires=' + exp.toGMTString()
      }
    },
    //预读取cookie中用户信息
    loadAccountInfo() {
      let self = this
      let accountInfo = self.getUserCookie('accountInfo')
      // 如果cookie里没有账号信息
      if (Boolean(accountInfo) === false) {
        // console.log('cookie中没有检测到用户账号信息！')
        return false
      } else {
        // 如果cookie里有账号信息
        // console.log('cookie中检测到账号信息！现在开始预填写！')
        let userEmail = ''
        let pwd = ''
        let index = accountInfo.indexOf('&')
        userEmail = accountInfo.substring(0, index)
        pwd = accountInfo.substring(index + 1) // 拿到加密后的密码
        // 解密
        let bytes = AES.decrypt(pwd.toString(), 'secret key 123')
        // 拿到解密后的密码（登录时输入的密码）
        let newPwd = bytes.toString(ENC)
        self.loginUser.email = userEmail
        self.loginUser.password = newPwd
        self.checked = true
      }
    },
  },
}
</script>


<style scoped>
.login {
  position: relative;
  width: 100%;
  height: 100%;
  background: url(../assets/login-bg.jpg) no-repeat center center;
  background-size: 100% 100%;
}
.form_container {
  width: 370px;
  height: 210px;
  position: absolute;
  top: 5%;
  left: 34%;
  padding: 25px;
  border-radius: 5px;
}
.manage_tip {
  text-align: center;
}
.manage_tip .title {
  font-family: 'Microsoft YaHei';
  font-weight: bold;
  font-size: 26px;
  color: #111;
}
.loginForm {
  margin-top: 20px;
  background-color: #fff;
  padding: 25px;
  border-radius: 5px;
  box-shadow: 0px 5px 10px #cccc;
}
.login_title {
  margin-bottom: 7px;
  text-align: center;
  color: #505458;
}
.verification {
  display: flex;
  justify-content: space-between;
}
.remember_pwd {
  text-align: left;
  margin-bottom: 20px;
  color: rgb(64, 158, 255);
}
.login_btn {
  margin-bottom: 20px;
}
.submit_btn {
  width: 100%;
}
.tip_area {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: #333;
}
.tip_area a {
  color: #409eff;
}
.el-form--label-top >>> .el-form-item__label {
  float: none;
  display: inline-block;
  text-align: left;
  padding: 0 0 0px;
}
</style>

<style>
.el-notification {
  width: auto !important;
}
.el-notification .el-icon-s-opportunity {
  color: #ffc107;
  font-size: 22px;
  margin-top: 2px;
}
.el-notification__title {
  margin: 5px 0px;
}
.el-notification__content {
  display: flex;
  width: 270px !important;
  justify-content: center;
}
</style>
```

### views/ForgotPassword.vue

```vue
<template>
  <div class="forgotpwd">
    <section class="form_title">
      <div class="manage_title">
        <span class="title">影片资金后台管理系统</span>
        <!-- 主要修改 :model rules ref v-model prop 添加 placeholder 与 html 代码无异-->
        <el-form
          :model="forgotPass"
          :rules="rules"
          ref="forgotPassForm"
          label-width="80px"
          class="registerForm"
          hide-required-asterisk
          @submit.native.prevent="forgetPass('forgotPassForm')"
        >
          <div class="forgot_title">
            <h3>忘记密码</h3>
          </div>
          <el-form-item label="邮箱" prop="email">
            <el-input
              type="email"
              v-model="forgotPass.email"
              prefix-icon="el-icon-message"
              placeholder="请输入邮箱"
            >
            </el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input
              :type="pwdType"
              v-model="forgotPass.password"
              prefix-icon="el-icon-lock"
              placeholder="请输入密码"
            >
              <i
                slot="suffix"
                title="显示或隐藏密码"
                :class="showPwd"
                :style="{ color: '#409eff', cursor: 'pointer' }"
                @click="togglePwd"
              >
              </i>
            </el-input>
          </el-form-item>
          <el-form-item label="确认密码" prop="password2">
            <el-input
              :type="pwdType"
              v-model="forgotPass.password2"
              prefix-icon="el-icon-lock"
              placeholder="请确认密码"
            >
              <i
                slot="suffix"
                title="显示或隐藏密码"
                :class="showPwd"
                :style="{ color: '#409eff', cursor: 'pointer' }"
                @click="togglePwd"
              >
              </i>
            </el-input>
          </el-form-item>
          <el-form-item label="验证码" prop="verifyCode">
            <div class="vertify">
              <el-input
                prefix-icon="el-icon-key"
                v-model="forgotPass.verifyCode"
                placeholder="验证码"
                auto-complete="off"
              ></el-input>
              <el-button
                type="primary"
                plain
                @click="getCode('forgotPassForm')"
                :disabled="isDisabled"
                class="emailcode"
              >
                {{ btnMsg }}
              </el-button>
            </div>
          </el-form-item>
          <div class="forgot_button">
            <el-button type="primary" native-type="submit" class="confirm"
              >确定</el-button
            >
          </div>
          <div class="tip_area">
            <router-link to="/">想起密码，登录</router-link>
            <p>注：若未收到验证码，请查看垃圾箱</p>
          </div>
        </el-form>
      </div>
    </section>
  </div>
</template>


<script>
export default {
  name: 'forgot',
  data() {
    const validatePass2 = (rule, value, callback) => {
      if (value !== this.forgotPass.password) {
        callback(new Error('两次输入密码不一致'))
      } else {
        callback()
      }
    }
    return {
      pwdType: 'password',
      isShowPwd: false,
      btnMsg: '获取邮箱验证码',
      isDisabled: false,
      time: 30,
      forgotPass: {
        email: '',
        password: '',
        password2: '',
        verifyCode: '',
      },
      rules: {
        email: [
          { required: true, message: '邮箱不能为空', trigger: 'blur' },
          { type: 'email', message: '请输入正确的邮箱地址', trigger: 'blur' },
        ],
        password: [
          { required: true, message: '密码不能为空', trigger: 'blur' },
          {
            min: 6,
            max: 10,
            message: '密码长度必须在6到10之间',
            trigger: 'blur',
          },
        ],
        password2: [
          { required: true, message: '确认密码不能为空', trigger: 'blur' },
          {
            min: 6,
            max: 10,
            message: '确认密码长度必须在6到10之间',
            trigger: 'blur',
          },
          { validator: validatePass2, trigger: 'blur' },
        ],
      },
    }
  },
  computed: {
    showPwd() {
      return this.isShowPwd ? 'third-icon-alieye' : 'third-icon-alino_eye'
    },
  },
  methods: {
    togglePwd() {
      this.pwdType === 'password'
        ? (this.pwdType = '')
        : (this.pwdType = 'password')
      this.isShowPwd = !this.isShowPwd
    },
    // 验证码发送
    getCode(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.$axios
            .get('/users/getEmailCode', {
              params: {
                email: this.forgotPass.email,
              },
            })
            .then((res) => {
              // console.log(res)
              if (res.data.succ) {
                this.$message({
                  message: '邮件已发送，请查收',
                  type: 'success',
                })
                this.isDisabled = true
                let interval = setInterval(() => {
                  this.btnMsg = '' + this.time + '秒'
                  --this.time
                  if (this.time < 0) {
                    this.btnMsg = '获取邮箱验证码'
                    this.time = 30
                    this.isDisabled = false
                    clearInterval(interval)
                  }
                }, 1000)
              }
            })
        }
      })
    },
    // 修改密码
    forgetPass(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.$axios
            .put('/users/resetPass', this.forgotPass)
            .then((res) => {
              // console.log(res)
              if (res.data.success) {
                this.$message({
                  message: res.data.message,
                  type: 'success',
                })
                // setTimeout(() => {
                //   history.pushState({}, '', '/login') //跳转到登录界面
                //   location.reload()
                // }, 1000)
                this.$router.push('/login')
              } else {
                this.$message({
                  message: res.data.message,
                  type: 'error',
                })
              }
            })
            .catch((err) => {
              this.$message({
                message: '系统错误!',
                type: 'error',
              })
              console.log('Error', err)
            })
        }
      })
    },
  },
}
</script>


<style scoped>
.forgotpwd {
  position: relative;
  width: 100%;
  height: 100%;
  background: url(../assets/login-bg.jpg) no-repeat center center;
  background-size: 100% 100%;
}
.form_title {
  width: 370px;
  height: 210px;
  position: absolute;
  top: 5%;
  left: 34%;
  padding: 25px;
  border-radius: 5px;
  text-align: center;
}
.form_title .manage_title .title {
  font-family: 'Microsoft YaHei';
  font-weight: bold;
  font-size: 26px;
  color: #111;
}
.forgot_title {
  margin-bottom: 20px;
  text-align: center;
  color: #505458;
}
.registerForm {
  margin-top: 20px;
  background-color: #fff;
  padding: 20px 40px 20px 20px;
  border-radius: 5px;
  box-shadow: 0px 5px 10px #cccc;
}
.vertify {
  display: flex;
  justify-content: space-between;
}
.vertify >>> .emailcode {
  margin-left: 10px;
  font-size: 10px;
}
.forgot_button {
  padding-left: 80px;
}
.confirm {
  width: 100%;
}
.tip_area {
  text-align: left;
  font-size: 12px;
  color: #333;
  margin-top: 20px;
}
.tip_area a {
  color: #409eff;
}
.tip_area p {
  margin-top: 15px;
  color: gray;
}
</style>
```

### views/Index.vue

```vue
<template>
  <div class="index">
    <Header-Nav></Header-Nav>
    <!-- 其实样式绑定放到 LeftMenu 的 <el-row> 也是一样的-->
    <Left-Menu :class="{ 'menu-collapse': collapse }"></Left-Menu>   
    <!-- 右侧内容区 -->
    <div class="rightContainer" :class="{ 'content-collapse': collapse }">
      <transition name="move" mode="out-in">
        <!-- 路由出口 -->
        <!-- 路由匹配到的组件将渲染在这里 -->
        <!-- router-view 渲染 Index.vue 的子路由组件 -->
        <router-view></router-view>
      </transition>
    </div>
  </div>
</template>


<script>
import HeaderNav from '../components/HeaderNav'
import LeftMenu from '../components/LeftMenu'
import bus from '../components/bus'
export default {
  name: 'index',
  components: {
    HeaderNav,
    LeftMenu,
  },
  data() {
    return {
      collapse: false,
    }
  },
  created() {
    bus.$on('collapse-content', (msg) => {
      this.collapse = msg
    })
  },
}
</script>


<style scoped>
.index {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
}


.rightContainer {
  position: absolute;
  top: 70px;
  left: 180px;
  right: 0;
  bottom: 0;
  transition: ease-in-out all 0.3s;
  overflow: auto;
}
.menu-collapse {
  width: 64px;
  /* 不能乱改折叠侧边栏收缩的宽度，否则展开宽度不够则会出现白边 */
}
.content-collapse {
  left: 64px;
  /* transition: ease-in-out all .3s; */
  /* transition: ease-in-out all .3s; */
  /*问题 控制右边内容宽度不超出 */
}
.move-enter,
.move-leave {
  opacity: 0;
  /* 显示/隐藏 */
}
.move-enter-active,
.move-leave-active {
  transition: opacity 0.3s;
  /* 过渡 慢慢显示/隐藏 */
}
</style>
```

### components/HeaderNav.vue

```vue
<template>
  <header class="head-nav">
    <el-row>
      <!-- 一行分为 24份 -->
      <!-- 一列占 6 份 -->
      <el-col :span="6" class="logo-container">
        <!-- 折叠按钮 -->
        <div class="collapse-btn" @click="collapseChage">
          <i v-if="collapse" class="el-icon-s-fold"></i>
          <i v-else class="el-icon-s-unfold"></i>
        </div>
        <img src="../assets/logo.png" alt="logo" class="logo" />
        <h1 class="title">影片资金后台管理系统</h1>
      </el-col>
       <!-- 一列占 6 份 -->
      <el-col :span="6" class="user">
        <div class="userinfo">
          <img class="avatar" :src="user.avatar" alt="avatar" />
          <div class="welcome">
            <p class="name comename">欢迎</p>
            <p class="name avatarname">{{ user.name }}</p>
          </div>
          <span class="username">
            <el-dropdown trigger="click" @command="setDialogInfo">
              <span class="el-dropdown-link">
                下拉菜单<i class="el-icon-caret-bottom el-icon--right"></i>
              </span>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item command="info">个人信息</el-dropdown-item>
                <el-dropdown-item command="logout">退出</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </span>
        </div>
      </el-col>
    </el-row>
  </header>
</template>


<script>
import bus from './bus'
export default {
  name: 'header-nav',
  data() {
    return {
      collapse: false,
    }
  },
  computed: {
    user() {
      return this.$store.getters.user
    },
  },
  methods: {
    // 侧边栏折叠
    collapseChage() {
      this.collapse = !this.collapse
      bus.$emit('collapse', this.collapse)
    },
    // 点击指令为 info 时调用 showInfo ，点击指令为 logout 时调用 logout
    setDialogInfo(cmdItem) {
      switch (cmdItem) {
        case 'info':
          this.showInfo()
          break
        case 'logout':
          this.logout()
          break
      }
    },
    showInfo() {
      // 跳转到个人信息页
      this.$router.push('/info')
    },
    logout() {
      bus.$emit('userLogout')
      bus.$emit('refreshCaptcha')
      // 清除 token
      localStorage.removeItem('eleToken')
      // 清除 vuex 的 state
      this.$store.dispatch('clearState')
      // 跳转到登录页
      this.$router.push('/login')
    },
  },
  mounted() {
    // 默认左侧菜单栏折叠
    if (document.body.clientWidth < 1500) {
      this.collapseChage()
    }
  },
}
</script>


<style scoped>
.head-nav {
  width: 100%;
  height: 60px;
  min-width: 600px;
  padding: 5px;
  background: #324057;
  color: #fff;
}
.collapse-btn {
  float: left;
  width: 22px;
  font-size: 18px;
  padding: 0 15px;
  cursor: pointer;
  line-height: 65px;
}
.logo-container {
  line-height: 60px;
  min-width: 400px;
}
.logo {
  height: 40px;
  width: 40px;
  margin-left: 7px;
  margin-right: 5px;
  vertical-align: middle;
  display: inline-block;
}
.title {
  display: inline-block;
  vertical-align: middle;
  font-size: 22px;
  font-family: 'Microsoft YaHei';
  letter-spacing: 3px;
}
.user {
  line-height: 60px;
  text-align: right;
  float: right;
  padding-right: 10px;
}
.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  vertical-align: middle;
  display: inline-block;
}
.welcome {
  display: inline-block;
  width: auto;
  vertical-align: middle;
  padding: 0 16px;
}
.name {
  line-height: 20px;
  text-align: center;
  font-size: 16px;
}
.comename {
  font-size: 16px;
}
.avatarname {
  color: #409eff;
  font-weight: bolder;
}
.username {
  cursor: pointer;
  margin-right: 5px;
}
.el-dropdown {
  color: #fff;
}
</style>
```

components/LeftMenu.vue

```vue
<template>
  <el-row class="menu_page">
    <el-col>
      <el-menu
        class="el-menu-vertical-demo"
        :collapse="collapse"
        background-color="#324057"
        text-color="#fff"
        active-text-color="#409eff"
        :unique-opened="false"
        mode="vertical"
        :collapse-transition="true"
      >
        <!-- 二级路由的数据结构：一个数组包含两个对象，两个对象有包含自己的子集（数组） -->
        <!-- 一级路由 -->
        <!-- 路由跳转到首页 -->       
        <router-link to="/home">
          <el-menu-item index="home">
            <i class="fa fa-margin el-icon-menu"></i>
            <span slot="title">首页</span>
          </el-menu-item>
        </router-link>
        <!-- 循环所有二级路由 -->
        <template v-for="item in items">
          <!-- 有子项的菜单 -->
          <el-submenu v-if="item.children" :index="item.path" :key="item.path">
            <template slot="title">
              <i :class="'fa fa-margin ' + item.icon"></i>
              <span slot="title">{{ item.name }}</span>
            </template>
            <!-- 显示二级路由的子项 -->
            <router-link
              v-for="(citem, cindex) in item.children"
              :to="citem.path"
              :key="cindex"
            >
              <el-menu-item :index="citem.path">
                <span slot="title">{{ citem.name }}</span>
              </el-menu-item>
            </router-link>
          </el-submenu>
        </template>
        <!-- 一级路由 -->
        <!-- 路由跳转到消息管理 -->    
        <router-link to="/tabs">
          <el-menu-item index="tabs">
            <i class="fa fa-margin el-icon-s-comment"></i>
            <span slot="title">消息</span>
          </el-menu-item>
        </router-link>
      </el-menu>
    </el-col>
  </el-row>
</template>


<script>
import bus from './bus'
export default {
  name: 'left-menu',
  data() {
    return {
      collapse: false,
      items: [
        {
          icon: 'el-icon-s-finance',
          name: '资金管理',
          path: 'fund',
          children: [{ path: 'fundlist', name: '资金流水' }],
        },
        {
          icon: 'el-icon-s-order',
          name: '信息管理',
          path: 'info',
          children: [{ path: 'info', name: '个人信息' }],
        },
      ],
    }
  },
  created() {
    // 通过 Event Bus 进行组件间通信，来折叠侧边栏
    bus.$on('collapse', (msg) => {
      this.collapse = msg
      bus.$emit('collapse-content', msg)
    })
  },
}
</script>


<style scoped>
.menu_page {
  position: absolute;
  top: 70px;
  left: 0;
  min-height: 600px;
  /*问题 一定要设置好最小高度，收缩侧边栏高度才不会变小 */
  background-color: #324057;
  /* z-index: 2; */
}
.el-menu {
  border: none;
}
.fa {
  font-size: 18px;
}
.fa-margin {
  margin-right: 5px;
}
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 180px;
  min-height: 600px;
  /*问题 一定要设置好宽度，展开侧边栏过渡动画才不会有白框的问题 */
  /*问题 一定要设置好最小高度，收缩侧边栏过渡动画才不会有上下不一致、缺边的问题 */
}
/*
不能乱改折叠侧边栏收缩的宽度，否则展开宽度不够则会出现白边
.el-menu-vertical-demo {
  width: 80px;
} */
.el-submenu .el-menu-item {
  min-width: 180px;
}


.hiddenDropdown,
.hiddenDropname {
  display: none;
}


a {
  text-decoration: none;
}
</style>
```

### components/bus.js

```js
import Vue from "vue"

// 使用 Event Bus
const bus = new Vue()
// 导出 bus
export default bus
```

### views/Home.vue

```vue
<template>
  <div class="home">
    <div class="container">
      <h1 class="title">电 影</h1>
      <p class="slogan"></p>
    </div>
  </div>
</template>

<script>
import Typed from 'typed.js'
export default {
  name: 'home',
  mounted() {
    let typed = new Typed('.slogan', {
      strings: ['窗户和镜子、故事和梦'],
      typeSpeed: 250,
      backSpeed: 250,
      loop: true,
    })
  },
}
</script>

<style scoped>
.home {
  width: 100%;
  height: 100%;
  background: url(../assets/showcase.jpg) no-repeat center center;
  background-size: 100% 100%;
  overflow: hidden;
}
.container {
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  padding-top: 100px;
  background-color: rgba(0, 0, 0, 0.7);
  text-align: center;
  color: white;
}
.title {
  font-size: 30px;
}
.slogan {
  display: inline-block;
  margin-top: 50px;
  font-size: 22px;
}
</style>
```

### utils/formatDate.js

```js
export function formatDate(value) {
  if (value === "") {
    //解决 value 为空传1970-01-01 00:00:00
    return ""
  } else {
    const d = new Date(value).toJSON()
    const date = new Date(+new Date(d) + 8 * 3600 * 1000)
      .toISOString()
      .replace(/T/g, " ")
      .replace(/\.[\d]{3}Z/, "")
    return date
  }
};
```

### views/FundList.vue

```vue
<template>
  <div class="fillContainer">
    <div class="fundList-content">
      <!-- 设置 inline 属性让表单域变为一行显示-->
      <el-form :inline="true" ref="add_data" :model="searchData">
        <el-form-item label="按时间筛选:">
          <el-date-picker
            v-model="searchData.startTime"
            type="datetime"
            placeholder="选择开始时间"
            @change="changeStart"
          >
          </el-date-picker>
          --
          <el-date-picker
            v-model="searchData.endTime"
            type="datetime"
            placeholder="选择结束时间"
            @change="changeEnd"
          >
          </el-date-picker>
          <el-button
            class="search"
            type="primary"
            size="small"
            @click="handleSearch"
            >筛选</el-button
          >
        </el-form-item>
        <el-form-item>
          <el-input placeholder="输入描述" v-model="search" clearable>
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="searchItem"
            ></el-button>
          </el-input>
        </el-form-item>
        <el-form-item class="btnRight">
          <el-button
            v-if="user.identity === 'manager'"
            type="primary"
            size="small"
            @click="handleAdd"
            >添加</el-button
          >
          <el-button
            v-if="user.identity === 'manager'"
            :disabled="!multipleSelectionFlag"
            type="danger"
            size="small"
            icon="el-icon-delete"
            class="handle-del ml10"
            @click="delAllSelection"
            >批量删除</el-button
          >
        </el-form-item>
      </el-form>
    </div>
    <!-- 设置最大高度 400 ，宽度 100% -->
    <!-- data 为显示的数据 -->
    <el-table
      v-if="tableData.length > 0"
      :default-sort="{ prop: 'date', order: 'ascending' }"
      max-height="400"
      border
      :data="tableData"
      style="width: 100%"
      @selection-change="handleSelectionChange"
      @row-click="rowClick"
      :row-style="rowStyle"
      :row-class-name="rowClassName"
      ref="multipleTable"
    >
      <el-table-column type="selection" width="50" align="center">
      </el-table-column>
      <el-table-column type="index" label="序号" width="50" align="center">
      </el-table-column>
      <el-table-column
        prop="date"
        label="创建时间"
        align="center"
        width="190"
        sortable
      >
        <template slot-scope="scope">
          <el-icon name="time"></el-icon>
          <span style="margin-left: 10px">{{ scope.row.date | showDate }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="type" label="收支类型" align="center">
      </el-table-column>
      <el-table-column prop="desc" label="收支描述" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.desc }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="income" label="收入" align="center">
        <template slot-scope="scope">
          <span style="color:#00d053">+ {{ scope.row.income }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="expenses" label="支出" align="center">
        <template slot-scope="scope">
          <span style="color:#f56767">- {{ scope.row.expenses }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="cash" label="账户现金" align="center">
        <template slot-scope="scope">
          <span style="color:#4db3ff">{{ scope.row.cash }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="remark" label="备注" align="center">
      </el-table-column>
      <el-table-column label="操作" align="center" fixed="right" width="150">
        <template slot-scope="scope">
          <el-button
            v-if="user.identity === 'manager'"
            size="small"
            type="info"
            @click="handleEdit(scope.$index, scope.row)"
            >编辑</el-button
          >
          <el-button
            v-if="user.identity === 'manager'"
            size="small"
            type="danger"
            @click="handleDelete(scope.$index, scope.row)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-row>
      <el-col :span="24">
        <div class="pagination">
          <el-pagination
            v-if="paginations.total > 0"
            :total="paginations.total"
            :page-size="paginations.page_size"
            :current-page.sync="paginations.page_index"
            :page-sizes="paginations.page_sizes"
            :layout="paginations.layout"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
          >
          </el-pagination>
        </div>
      </el-col>
    </el-row>
    <Add-Dialog
      :dialog="dialog"
      :formData="formData"
      @update="getFundList"
    ></Add-Dialog>
  </div>
</template>


<script>
import AddDialog from '../components/AddDialog'
import { formatDate } from '../utils/formatDate'
// import bus from '../components/bus'
export default {
  name: 'fundlist',
  components: {
    AddDialog,
  },
  computed: {
    user() {
      return this.$store.getters.user
    },
  },
  data() {
    return {
      // 根据日期搜索的时间数据
      searchData: {
        startTime: '',
        endTime: '',
      },
      // 默认是否折叠
      // collapse: false,
      // 搜索描述字符串
      search: '',
      // start: false,
      // end: false,
      // 搜索（过滤）表单的数据
      filterTableData: [],
      // 所有分页的数据
      allTableData: [],
      // 当前分页的数据
      tableData: [],
      // 删除的表单数据
      delList: [],
      // 选中的表格行
      selectionRow: [],
      multipleSelection: [],
      multipleSelectionFlag: false,
      // 表单数据
      formData: {
        type: '',
        desc: '',
        income: '',
        expenses: '',
        cash: '',
        remark: '',
        id: '',
      },
      // 弹出框默认为编辑弹出框且不显示
      dialog: {
        show: false,
        title: '',
        option: 'edit',
      },
      // 分页器
      paginations: {
        total: 0,      // 总页数
        page_size: 5,  // 一页显示多少条
        page_index: 1, // 当前位于哪页，默认显示第一页
        page_sizes: [5, 10, 15, 20], // 选择一页显示多少条
        layout: 'total, sizes, prev, pager, next, jumper', // 组件分页布局
      },
    }
  },
  // 创建组件时显示资金流水
  created() {
    this.getFundList()
  },
  // 当搜索描述没有值时刷新表单，watch专门处理异步
  watch: {
    search: function(newVal) {
      if (newVal === '') {
        this.getFundList()
      }
    },
  },
  methods: {
    // 获取列表数据
    getFundList() {
      this.$axios
        .get('/profiles')
        .then((res) => {
          this.allTableData = res.data
          // 先获取所有数据作为要过滤的数据
          this.filterTableData = res.data
          // console.log(res.data)
          // 显示分页数据
          this.setPagination()
        })
        .catch((err) => console.log(err))
    },
    // 初始化第一页数据和分页器数据
    setPagination() {
      // 根据表单的数据总数来决定分页数据的总数
      this.paginations.total = this.allTableData.length
      // 初始一页显示的数据条数为 5
      this.paginations.page_size = 5
      // 初始分页数据为第一页数据
      this.tableData = this.allTableData.filter((item, index) => {
        return index < this.paginations.page_size
      })
      // console.log(this.tableData)
      // 当跳转到其他分页时执行 handleCurrentChange
      this.handleCurrentChange(this.paginations.page_index)
    },
    // page 前往第几页的页数，即当前页
    handleCurrentChange(page) {
      // console.log(this.paginations.page_index)
      // 获取当前页（第 page 页）的第一条数据 第一页第一条数据：5 * 0 = 0，第二页第一条数据：5 * 1 = 5
      let index = this.paginations.page_size * (page - 1)
      // 获取当前页（第 page 页）最大条数（即下一页第一条数据） 第一页数据最大条数：5 * 1 = 5
      let nums = this.paginations.page_size * page
      // tables 数组当前页的所有数据
      let tables = []
      for (let i = index; i < nums; i++) {
        // 如果 this.allTableData 有当前页的数据，则存放在 tables 里
        if (this.allTableData[i]) {
          tables.push(this.allTableData[i])
        }
      }
      this.tableData = tables
    },
    // 过滤日期格式
    filters: {
      showDate: function(value) {
        let date = new Date(value * 1000)
        return formatDate(value)
      },
    },
    // size 切换一页显示多少条的条数 例如 5 条切换为 10 条
    handleSizeChange(size) {
      this.paginations.page_index = 1
      this.paginations.page_size = size
      // 初始分页数据为第一页数据
      this.tableData = this.allTableData.filter((item, index) => {
        return index < size
      })
    },
    // 当选择项发生变化时会触发选择高亮操作
    handleSelectionChange(val) {
      this.selectionRow = val      // 数组保存已选择行
      this.multipleSelection = val // 数组保存已选择行
      // console.log(this.multipleSelection)
      if (this.multipleSelection.length > 1) {
        this.multipleSelectionFlag = true
      }
      if (this.multipleSelection.length === 0) {
        this.multipleSelectionFlag = false
      }
    },
    // 高亮选中行
    rowClick(row, column, event) {
      let refsTable = this.$refs.multipleTable // 获取表格对象
      let findRow = this.selectionRow.find(
        (item) => item.rowIndex === row.rowIndex
      )
      if (findRow) {
        refsTable.toggleRowSelection(row, false)
        return
      }
      refsTable.clearSelection()
      refsTable.toggleRowSelection(row) // 调用选中行方法
    },
    rowStyle({ row, rowIndex }) {
      Object.defineProperty(row, 'rowIndex', {
        //给每一行添加不可枚举属性 rowIndex 来标识当前行
        value: rowIndex,
        writable: true,
        enumerable: false,
      })
    },
    rowClassName({ row, rowIndex }) {
      let rowName = '',
        findRow = this.selectionRow.find((c) => c.rowIndex === row.rowIndex)
      if (findRow) {
        rowName = 'current-row ' // elementUI 默认高亮行的class类 不用再样式了^-^,也可通过css覆盖改变背景颜色
      }
      return rowName //也可以再加上其他类名 如果有需求的话
    },
    // 添加操作
    handleAdd() {
      this.dialog.show = true
      this.dialog = {
        show: true,
        title: '添加资金信息',
        option: 'add',
      }
      this.formData = {
        type: '',
        desc: '',
        income: '',
        expenses: '',
        cash: '',
        remark: '',
        _id: '',
      }
    },
    // 编辑操作
    handleEdit(index, row) {
      // 编辑弹出框
      this.dialog = {
        show: true,
        title: '修改资金信息',
        option: 'edit',
      }
      this.formData = {
        type: row.type,
        desc: row.desc,
        income: row.income,
        expenses: row.expenses,
        cash: row.cash,
        remark: row.remark,
        _id: row._id,
      }
    },
    // 删除操作
    handleDelete(index, row) {
      // 二次确认删除
      this.$confirm('确定要删除吗？', '提示', {
        type: 'warning',
        cancelButtonClass: 'btn-custom-cancel',
        confirmButtonText: '确定',
        cancelButtonText: '取消',
      })
        .then(() => {
          this.$axios.delete(`/profiles/delete/${row._id}`).then((res) => {
            this.$message({
              message: '数据删除成功',
              type: 'success',
            })
            this.jumpRefresh()
          })
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除',
          })
        })
    },
    // 批量删除操作
    delAllSelection() {
      // 二次确认删除
      this.$confirm('确定要批量删除吗？', '提示', {
        type: 'warning',
        cancelButtonClass: 'btn-custom-cancel',
        confirmButtonText: '确定',
        cancelButtonText: '取消',
      }).then(() => {
        let checkArr = this.multipleSelection // multipleSelection存储了勾选到的数据
        let params = []
        checkArr.forEach(function(item) {
          params.push(item._id) // 添加所有需要删除数据的id到一个数组，post提交过去
        })
        this.$axios
          .post('/profiles/multiDelete', params)
          .then((res) => {
            this.$message({
              message: '数据批量删除成功',
              type: 'success',
            })
            this.jumpRefresh()
          })
          .catch(() => {
            this.$message({
              type: 'info',
              message: '已取消删除',
            })
          })
      })
    },
    // 删除后自动跳转刷新
    jumpRefresh() {
      // totalPage 获取删除后的总页数，Math.ceil 返回大于或等于一个给定数字的最小整数
      // const totalPage = Math.ceil(
      //   (this.allTableData.length - 1) / this.paginations.page_size
      // )
      // // pageNum 获取删除后要跳转的页码 如果当前页码大于总页数，则删除后要跳转的页码为总页数
      // const pageNum =
      //   this.paginations.page_index > totalPage
      //     ? totalPage
      //     : this.paginations.page_index
      // // 删除后要跳转的页码只可以大于或等于 1
      // this.pageIndex = pageNum < 1 ? 1 : pageNum
      this.getFundList()
      this.handleCurrentChange(this.paginations.page_index)
      this.getFundList()
    },    
    // 按时间筛选操作
    handleSearch() {
      if (!this.searchData.startTime || !this.searchData.endTime) {
        this.$message({
          type: 'warning',
          message: '请选择时间区间',
        })
        this.getFundList()
        return
      } else {
        // 获取开始时间（毫秒数）
        const sTime = this.searchData.startTime.getTime()
        // 获取结束时间（毫秒数）
        const eTime = this.searchData.endTime.getTime()
        // 过滤数据
        this.allTableData = this.filterTableData.filter((item) => {
          // 获取每条数据的毫秒数
          let date = new Date(item.date)
          let time = date.getTime()
          // 筛选出大于等于开始时间并小于等于结束时间的数据
          return time >= sTime && time <= eTime
        })
        this.setPagination()
      }
    },
    // 当开始时间为空时则刷新列表
    changeStart(val) {
      if (val === null) {
        val = ''
        this.getFundList()
      }
    },
    // 当结束时间为空时则刷新列表
    changeEnd(val) {
      if (val === null) {
        val = ''
        this.getFundList()
      }
    },      
    // 搜索描述
    searchItem() {
      // 如果没有输入内容则提示
      if (!this.search) {
        this.$message({
          type: 'warning',
          message: '请输入描述',
        })
        this.getFundList()
        return
      } else {
        this.allTableData = this.filterTableData.filter((data) =>
          data.desc.toLowerCase().includes(this.search.toLowerCase())
        )
        // 如果没有搜索到内容则提示
        if (this.allTableData.length === 0) {
          this.$message({
            type: 'warning',
            message: '没有搜索结果',
          })
          this.search = ''
          this.getFundList()
        }
        this.setPagination()
      }
    },
  },
}
</script>


<style scoped>
.fillContainer {
  width: 100%;
  height: 100%;
  padding: 16px;
  box-sizing: border-box;
}
.fundList-content {
  padding: 10px;
}
.btnRight {
  float: right;
}
.pagination {
  text-align: right;
  margin-top: 10px;
}
.search {
  margin-left: 10px;
}
.ml10 {
  margin-left: 10px;
}
</style>
<style>
.btn-custom-cancel {
  float: right;
  margin-left: 10px;
}
</style>
```

### components/AddDialog.vue

```vue
<template>
  <div class="dialog">
    <el-dialog
      :title="dialog.title"
      :visible.sync="dialog.show"
      :close-on-click-modal="false"
      :close-on-press-escape="false"
      :modal-append-to-body="false"
      class="above_dialog"
    >
      <div class="form">
        <el-form
          ref="form"
          :model="formData"
          :rules="form_rules"
          label-width="120px"
          style="margin:10px"
        >
          <el-formItem label="收支类型:">
            <el-select v-model="formData.type" placeholder="收支类型">
              <el-option
                v-for="(formtype, index) in form_type_list"
                :key="index"
                :label="formtype"
                :value="formtype"
              >
              </el-option>
            </el-select>
          </el-formItem>


          <el-formItem label="收支描述:" prop="desc">
            <el-input type="desc" v-model="formData.desc"></el-input>
          </el-formItem>


          <el-form-item label="收入:" prop="income">
            <el-input type="income" v-model="formData.income"></el-input>
          </el-form-item>


          <el-form-item label="支出:" prop="expenses">
            <el-input type="expenses" v-model="formData.expenses"></el-input>
          </el-form-item>


          <el-form-item label="账户现金:" prop="cash">
            <el-input type="cash" v-model="formData.cash"></el-input>
          </el-form-item>


          <el-form-item label="备注:">
            <el-input
              type="textarea remark"
              v-model="formData.remark"
            ></el-input>
          </el-form-item>


          <el-form-item class="">
            <el-button type="primary" @click="submitForm('form')"
              >提 交</el-button
            >
            <el-button @click="dialog.show = false">取 消</el-button>
          </el-form-item>
        </el-form>
      </div>
    </el-dialog>
  </div>
</template>


<script>
export default {
  // 问题 组件名不可以与 html元素同名
  name: 'add-dialog',
  props: {
    dialog: Object,
    formData: Object,
  },
  data() {
    return {
      form_type_list: [
        '电影',
        '提现',
        '提现手续费',
        '充值',
        '优惠券',
        '充值礼券',
        '转账',
      ],
      form_rules: {
        desc: [
          { required: true, message: '收支描述不能为空！', trigger: 'blur' },
        ],
        income: [
          { required: true, message: '收入不能为空！', trigger: 'blur' },
        ],
        expenses: [
          { required: true, message: '支出不能为空！', trigger: 'blur' },
        ],
        cash: [
          { required: true, message: '账户现金不能为空！', trigger: 'blur' },
        ],
      },
      message: '',
    }
  },
  methods: {
    submitForm(form) {
      this.$refs[form].validate((valid) => {
        if (valid) {
          // console.log(this.formData)
          const url =
            this.dialog.option === 'add' ? 'add' : `edit/${this.formData._id}`
          this.message = url === 'add' ? '添加' : '编辑'
          this.$axios
            .post(`/profiles/${url}`, this.formData)
            .then((res) => {
              this.$message({
                message: `数据${this.message}成功`,
                type: 'success',
              })
              this.dialog.show = false
              this.$emit('update')
            })
        }
      })
    },
  },
}
</script>

<style scoped>
.above_dialog {
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}
.form {
  height: 70%;
}
</style>
// 修改 element UI 默认样式
<style>
.el-dialog__body {
  padding: 6px 20px;
}
</style>
```

### components/Echars/barEcharts.vue

```vue
<template>
  <div>
    <div :id="id" :style="{ width: width, height: height }"></div>
  </div>
</template>

<script>
// let echarts = require('echarts/lib/echarts')
// 引入需要使用的图表类型，标题，提示信息等
// require('echarts/lib/chart/bar')
// require('echarts/lib/component/legend')
// require('echarts/lib/component/title')


export default {
  name: 'barEchart',
  props: {
    id: {
      type: String,
      default: 'myChart',
    },
    width: {
      type: String,
      default: '100%',
    },
    height: {
      type: String,
      default: '100%',
    },
  },
  data() {
    return {
      chart: null,
    }
  },
  mounted() {
    this.initChart()
  },
  methods: {
    initChart() {
      this.chart = echarts.init(document.getElementById(this.id))
      this.chart.setOption({
        title: {
          text: '最近一周销售统计',
          left: 'center',
          top: 2,
          textStyle: {
            fontSize: 20,
          },
        },
        color: ['#4A90E2', '#F5A623', '#FF5858', '#64D572'],
        grid: {
          left: '3%',
          right: '4%',
          bottom: '10%',
          containLabel: true,
        },
        legend: {
          left: 'center',
          bottom: '2%',
          data: ['可口可乐', '爆米花', '薯片'],
        },
        xAxis: [
          {
            type: 'category',
            data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日'],
            axisTick: {
              alignWithLabel: true,
            },
          },
        ],
        yAxis: [
          {
            type: 'value',
          },
        ],
        series: [
          {
            name: '可口可乐',
            type: 'bar',
            data: [10, 52, 200, 334, 390, 330, 220],
            itemStyle: {
              normal: {
                label: {
                  show: true, //开启显示
                  position: 'top', //在上方显示
                  textStyle: { //数值样式
                    color: '#4A90E2',
                    fontSize: 12
                  }
                }
              }
            },
          },
          {
            name: '爆米花',
            type: 'bar',
            data: [10, 52, 200, 334, 390, 330, 220],
            itemStyle: {
              normal: {
                label: {
                  show: true, //开启显示
                  position: 'top', //在上方显示
                  textStyle: { //数值样式
                    color: '#F5A623',
                    fontSize: 12
                  }
                }
              }
            },
          },
          {
            name: '薯片',
            type: 'bar',
            data: [10, 52, 200, 334, 390, 330, 220],
            itemStyle: {
              normal: {
                label: {
                  show: true, //开启显示
                  position: 'top', //在上方显示
                  textStyle: { //数值样式
                    color: '#FF5858',
                    fontSize: 12
                  }
                }
              }
            },
          }          
        ],
      })
    },
  },
}
</script>

<style scoped>
</style>
```

### views/Info.vue

```vue
<template>
  <div class="info">
   <el-row :gutter="20">
     <el-col :span="8">
         <el-card shadow="hover" class="mgb20" style="height:252px;">
             <div class="user-info">
                 <img :src="avatar" class="user-avatar" alt />
                 <div class="user-info-container">
                   <div class="placeholder no-content">
                   </div>
                   <div class="user-info-cont">
                     <div class="user-info-name">{{user}}</div>
                     <div>{{role}}</div>
                   </div>
                   <el-upload
                     class="upload"
                     :action="uploadUrl + id"
                     :on-success="uploadImg"
                     :before-upload="beforeImgUpload"
                     :show-file-list="false"
                     multiple>
                     <el-button size="small" type="primary"><i class="el-icon-upload"></i> 更换头像</el-button>
                   </el-upload>  
                 </div>
             </div>
             <div class="user-info-list">
                 上次登录时间：
                 <span>2020-01-01</span>
             </div>
             <div class="user-info-list">
                 上次登录地点：
                 <span>广州</span>
             </div>
         </el-card>
         <el-card shadow="hover" style="height:252px;">
             <div slot="header" class="clearfix">
                 <span>语言详情</span>
             </div>Vue
             <el-progress :percentage="71.3" color="#42b983"></el-progress>JavaScript
             <el-progress :percentage="24.1" color="#f1e05a"></el-progress>CSS
             <el-progress :percentage="13.7"></el-progress>HTML
             <el-progress :percentage="5.9" color="#f56c6c"></el-progress>
         </el-card>
     </el-col>
     <el-col :span="16">
         <el-row :gutter="20" class="mgb20">
             <el-col :span="8">
                 <el-card shadow="hover" :body-style="{padding: '0px'}">
                     <div class="grid-content grid-con-1">
                         <i class="el-icon-user grid-con-icon"></i>
                         <div class="grid-cont-right">
                             <div class="grid-num">1234</div>
                             <div>用户访问量</div>
                         </div>
                     </div>
                 </el-card>
             </el-col>
             <el-col :span="8">
                 <el-card shadow="hover" :body-style="{padding: '0px'}">
                     <div class="grid-content grid-con-2">
                         <i class="el-icon-bell grid-con-icon"></i>
                         <div class="grid-cont-right">
                             <div class="grid-num">321</div>
                             <div>系统消息</div>
                         </div>
                     </div>
                 </el-card>
             </el-col>
             <el-col :span="8">
                 <el-card shadow="hover" :body-style="{padding: '0px'}">
                     <div class="grid-content grid-con-3">
                         <i class="el-icon-goods grid-con-icon"></i>
                         <div class="grid-cont-right">
                             <div class="grid-num">5000</div>
                             <div>数量</div>
                         </div>
                     </div>
                 </el-card>
             </el-col>
        </el-row>     
        <el-row :gutter="20" class="mgb20">
          <el-col :span="24">
            <el-card shadow="hover">
              <bar-echart id="barEcharts" height="365px" ref="echarts"></bar-echart>
            </el-card>
          </el-col>
        </el-row>
      </el-col>       
    </el-row>
  </div>
</template>

<script>
  import BarEchart from '../components/ECharts/barEchart'
  export default {
    name: 'info',
    components: {
      BarEchart
    },
    data() {
      return {
        uploadUrl: 'http://47.115.15.21:5001/api/users/updateAvatar/'
      }
    },
    computed: {
      avatar() {
        return this.$store.getters.user.avatar
      },
      user() {
        return this.$store.getters.user.identity === 'manager' ? 'admin' : 'staff'
      },
      role() {
        return this.$store.getters.user.identity === 'manager' ? '管理员' : '普通员工'
      },
      id() {
        return this.$store.getters.user.id
      }
    },
    mounted () {
      this.selfAdaption()
    },
    methods: {
      uploadImg(res, file) {
        // console.log(res)
        // console.log(file)
        this.$store.getters.user.avatar = res.url
        this.$message({type: 'success', message: '更换头像成功'})  
      },      
      beforeImgUpload(file) {
        const isRightType = (file.type === 'image/jpeg') || (file.type === 'image/png') || (file.type === 'image/gif');
        const isLt2M = file.size / 1024 / 1024 < 2;
        if (!isRightType) {
            this.$message.error('上传头像图片只能是 JPG/PNG/GIF 格式!');
        }
        if (!isLt2M) {
            this.$message.error('上传头像图片大小不能超过 2MB!');
        }
        return isRightType && isLt2M;
      },
      // echart自适应
      selfAdaption () {
        let that = this
        setTimeout(() => {
          window.onresize = function () {
            if (that.$refs.echarts) {
              that.$refs.echarts.chart.resize()
            }
          }
        }, 10)
      }
    }
  }
</script>

<style scoped>
.user-info-container {
  display: flex;
  height: 120px;
  justify-content: space-between;
  flex-direction: column;
  padding-left: 50px;
}
.info {
  width: 100%;
  height: 100%;
  background-color: #eee;
  padding: 20px;
  box-sizing: border-box;
  overflow: hidden;
}
.el-row {
    margin-bottom: 20px;
}


.grid-content {
    display: flex;
    align-items: center;
    height: 100px;
}


.grid-cont-right {
    flex: 1;
    text-align: center;
    font-size: 14px;
    color: #999;
}


.grid-num {
    font-size: 30px;
    font-weight: bold;
}


.grid-con-icon {
    font-size: 50px;
    width: 100px;
    height: 100px;
    text-align: center;
    line-height: 100px;
    color: #fff;
}
.grid-num {
  margin-bottom: 5px;
}
.grid-con-1 .grid-con-icon {
    background: rgb(45, 140, 240);
}


.grid-con-1 .grid-num {
    color: rgb(45, 140, 240);
}


.grid-con-2 .grid-con-icon {
    background: rgb(100, 213, 114);
}


.grid-con-2 .grid-num {
    color: rgb(45, 140, 240);
}


.grid-con-3 .grid-con-icon {
    background: rgb(242, 94, 67);
}


.grid-con-3 .grid-num {
    color: rgb(242, 94, 67);
}


.user-info {
    display: flex;
    align-items: center;
    padding-bottom: 20px;
    border-bottom: 2px solid #ccc;
    margin-bottom: 20px;
}


.user-avatar {
    width: 120px;
    height: 120px;
    border-radius: 50%;
}


.user-info-cont {
    font-size: 16px solid;
    color: #999;
}


.user-info-cont div:first-child {
    font-size: 26px;
    margin-bottom: 5px;
    color: #222;
}


.user-info-list {
    font-size: 14px;
    color: #999;
    line-height: 25px;
}


.user-info-list span {
    margin-left: 70px;
}


.mgb20 {
    margin-bottom: 20px;
}
</style>
```

### views/Tabs.vue

```vue
<template>
  <div class="message-container">
    <div class="message-content">
      <el-tabs v-model="message">
        <el-tab-pane :label="`未读消息(${unread.length})`" name="first">
          <el-table :data="unread" :show-header="false" style="width: 100%">
            <el-table-column>
              <template slot-scope="scope">
                <span class="message-title">{{ scope.row.title }}</span>
              </template>
            </el-table-column>
            <el-table-column prop="date" width="180"></el-table-column>
            <el-table-column width="120">
              <template slot-scope="scope">
                <el-button size="small" @click="handleRead(scope.$index)"
                  >标为已读</el-button
                >
              </template>
            </el-table-column>
          </el-table>
          <div class="handle-row">
            <el-button type="primary">全部标为已读</el-button>
          </div>
        </el-tab-pane>
        <el-tab-pane :label="`已读消息(${read.length})`" name="second">
          <template v-if="message === 'second'">
            <el-table :data="read" :show-header="false" style="width: 100%">
              <el-table-column>
                <template slot-scope="scope">
                  <span class="message-title">{{ scope.row.title }}</span>
                </template>
              </el-table-column>
              <el-table-column prop="date" width="150"></el-table-column>
              <el-table-column width="120">
                <template slot-scope="scope">
                  <el-button type="danger" @click="handleDel(scope.$index)"
                    >删除</el-button
                  >
                </template>
              </el-table-column>
            </el-table>
            <div class="handle-row">
              <el-button type="danger">删除全部</el-button>
            </div>
          </template>
        </el-tab-pane>
        <el-tab-pane :label="`回收站(${recycle.length})`" name="third">
          <template v-if="message === 'third'">
            <el-table :data="recycle" :show-header="false" style="width: 100%">
              <el-table-column>
                <template slot-scope="scope">
                  <span class="message-title">{{ scope.row.title }}</span>
                </template>
              </el-table-column>
              <el-table-column prop="date" width="150"></el-table-column>
              <el-table-column width="120">
                <template slot-scope="scope">
                  <el-button @click="handleRestore(scope.$index)"
                    >还原</el-button
                  >
                </template>
              </el-table-column>
            </el-table>
            <div class="handle-row">
              <el-button type="danger">清空回收站</el-button>
            </div>
          </template>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>


<script>
export default {
  name: 'tabs',
  data() {
    return {
      message: 'first',
      showHeader: false,
      unread: [
        {
          date: '2018-04-19 20:00:00',
          title: '【系统通知】该系统将于今晚凌晨2点到5点进行升级维护',
        },
        {
          date: '2018-04-19 21:00:00',
          title: '今晚12点整发大红包，先到先得',
        },
      ],
      read: [
        {
          date: '2018-04-19 20:00:00',
          title: '【系统通知】该系统将于今晚凌晨2点到5点进行升级维护',
        },
      ],
      recycle: [
        {
          date: '2018-04-19 20:00:00',
          title: '【系统通知】该系统将于今晚凌晨2点到5点进行升级维护',
        },
      ],
    }
  },
  methods: {
    handleRead(index) {
      const item = this.unread.splice(index, 1)
      console.log(item)
      this.read = item.concat(this.read)
    },
    handleDel(index) {
      const item = this.read.splice(index, 1)
      this.recycle = item.concat(this.recycle)
    },
    handleRestore(index) {
      const item = this.recycle.splice(index, 1)
      this.read = item.concat(this.read)
    },
  },
  computed: {
    unreadNum() {
      return this.unread.length
    },
  },
}
</script>

<style>
.message-container {
  width: auto;
  height: 100%;
  padding: 10px;
  box-sizing: border-box;
  background: #f0f0f0;
  border: 1px solid #ddd;
  border-radius: 5px;
}
.message-content {
  padding: 30px;
  background: #fff;
}
.message-title {
  cursor: pointer;
}
.handle-row {
  margin-top: 30px;
}
</style>
```

## 表单基本用法

1. el-form 容器，通过 model 绑定整个表单数据源，inline 使表单在一行排列，否则换行排列

2. el-form-item 容器，通过 label 绑定标签

3. 表单组件通过 v-model 绑定 model 中的数据，与 el-form 数据源对应

```vue
<el-form inline :model="data">
  <el-form-item label="审批人">
    <el-input v-model="data.user" placeholder="审批人"></el-input>
  </el-form-item>
<el-form>
```

```vue
<template>
  <div id="app">
    <el-form inline :model="data">
      <el-form-item label="审批人">
        <el-input v-model="data.user" placeholder="审批人"></el-input>
      </el-form-item>
      <el-form-item label="活动区域">
        <el-select v-model="data.region" placeholder="活动区域">
          <el-option label="区域一" value="shanghai"></el-option>
          <el-option label="区域二" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">查询</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      data: {
        user: 'sam',
        region: '区域二'
      }
    }
  },
  methods: {
    onSubmit() {
      console.log(this.data)
    }
  }}
</script>
```

## 表单校验基本用法

1. 定义校验规则，可以绑定到 el-form 或 el-form-item

```js
data() {
  const userValidator = (rule, value, callback) => {
    if (value.length > 3) {
      callback()
    } else {
      callback(new Error('用户名长度必须大于3'))
    }
  }
  return {
    data: {
      user: 'sam',
      region: '区域二'
    },
    rules: {
      user: [
        { required: true, trigger: 'change', message: '用户名必须录入' },
        { validator: userValidator, trigger: 'change' }
      ]
    }
  }
}
```

2. 指定 el-form-item 的 prop 属性

```vue
<el-form-item label="审批人" prop="user">
  <el-input v-model="data.user" placeholder="审批人" clearable></el-input>
</el-form-item>
```

## 表单校验相关属性

● hide-required-asterisk：隐藏必录标识

● inline-message：验证消息是否在一行显示

## 表单校验高级用法

### 用法一：动态改变校验规则

1、rules 只包含一个校验规则

```js
{
  rules: {
      user: [
        { required: true, trigger: 'change', message: '用户名必须录入' },
      ]
  }
}
```

2、动态添加 rules

```js
addRule() {
    const userValidator = (rule, value, callback) => {
      if (value.length > 3) {
        this.inputError = ''
        this.inputValidateStatus = ''
        callback()
      } else {
        callback(new Error('用户名长度必须大于3'))
      }
    }
    const newRule = [
      ...this.rules.user,
      { validator: userValidator, trigger: 'change' }
    ]
    this.rules = Object.assign({}, this.rules, { user: newRule })
}
```

### 用法二：手动控制校验状态

● validate-status：验证状态，枚举值，共四种：

1、success：验证成功

2、error：验证失败

3、validating：验证中

4、 ( 空)：未验证

● error：自定义错误提示

设置 el-form-item 属性

```vue
<el-form-item
  label="用户名"
  prop="user"
  :error="error"
  :validate-status="status"
>
<!-- ... -->
</el-form-item>
```

自定义 status 和 error

```js
showError() {
  this.status = 'error'
  this.error = '用户名输入有误'
},
showSuccess() {
  this.status = 'success'
  this.error = ''
},
showValidating() {
  this.status = 'validating'
  this.error = ''
}
```

## 表单属性解析

● label-position：标签位置，枚举值，left 和 top

● label-width：标签宽度

● label-suffix：标签后缀

● inline：行内表单

● disabled: 设置整个 form 中的表单组件全部 disabled，优先级低于表单组件自身的 disabled

```js
/* el-input 源码 */
inputDisabled() {
  return this.disabled || (this.elForm || {}).disabled;
}
```

● size：设置表单组件尺寸

```js
/* el-input */
inputSize() {
  return this.size || this._elFormItemSize || (this.$ELEMENT || {}).size;
},
_elFormItemSize() {
  return (this.elFormItem || {}).elFormItemSize;
}
/* el-form-item */
elFormItemSize() {
  return this.size || this._formSize;
},
_formSize() {
  return this.elForm.size;
}
```

### 案例

```vue
<template>
  <div id="app">
    <el-form
        :model="data"
        style="width: 500px"
        label-position="left"
        label-width="100px"
        label-suffix="："
        :inline="false"
        :rules="rules"
        :disabled="false"
        status-icon
        validate-on-rule-change
        hide-required-asterisk
        :inline-message="false"
    >
      <el-form-item
          label="用户名"
          prop="user"
          :error="error"
          :validate-status="status"
      >
        <el-input v-model="data.user" placeholder="用户名" clearable></el-input>
      </el-form-item>
      <el-form-item label="活动区域" prop="region">
        <el-select v-model="data.region" placeholder="活动区域" style="width:100%">
          <el-option label="区域一" value="shanghai"></el-option>
          <el-option label="区域二" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">查询</el-button>
        <el-button type="primary" @click="addRule">添加校验规则</el-button>
        <el-button @click="showError">错误状态</el-button>
        <el-button @click="showSuccess">正确状态</el-button>
        <el-button @click="showValidating">验证状态</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>


<script>
  export default {
    name: 'app',
    data() {
      return {
        data: {
          user: 'sam',
          region: '区域二'
        },
        error: '',
        status: '',
        rules: {
          user: [
            { required: true, trigger: 'change', message: '用户名必须录入' }
          ]
        }
      }
    },
    methods: {
      /* eslint-disable */
      onSubmit() {
        console.log(this.data)
      },
      addRule() {
        const userValidator = (rule, value, callback) => {
          if (value.length > 3) {
            this.inputError = ''
            this.inputValidateStatus = ''
            callback()
          } else {
            callback(new Error('用户名长度必须大于3'))
          }
        }
        const newRule = [
          ...this.rules.user,
          { validator: userValidator, trigger: 'change' }
        ]
        this.rules = Object.assign({}, this.rules, { user: newRule })
      },
      showError() {
        this.status = 'error'
        this.error = '用户名输入有误'
      },
      showSuccess() {
        this.status = 'success'
        this.error = ''
      },
      showValidating() {
        this.status = 'validating'
        this.error = ''
      }
    }
  }
</script>
```























































































































































































































