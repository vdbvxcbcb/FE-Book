## 全局过滤器

filter.wxs

```js
/**全局过滤器
 * @author jayhao
 * @date 2019-12-14
 * 使用：具体按实际引入路径为准
 * 1、引入文件<wxs module="filters" src="../../filter.wxs"></wxs>
 */
var filters = {
  /**格式化金额
   * @params money 金额
   * @author jayhao
   * @date 2019-12-14
   * 使用: filters.keepDecimal(100)
   * 数字保留两位小数、小数点后两位为0则截取
   */
  keepDecimal: function (money) {
    if (!money) return money

    var rescast = money.toString();
    var obj = rescast.split('.');
    // 保留两位小数
    if (obj.length == 2 && obj[1] != '00') {
      return Number(rescast).toFixed(2) * 1;
    }
    // 保留整数
    if (obj.length == 2 && obj[1] == '00') {
      return Number(rescast).toFixed(0) * 1;
    }

    return Number(rescast);
  },
  /**
 * 将数字（整数）转为汉字
 * @param num 
 * @description 从零到一亿亿，需要小数的可自行截取小数点后面的数字直接替换对应arr1的读法就行了
 */
  convertToChinaNum: function (num) {
    var arr1 = ['零', '一', '二', '三', '四', '五', '六', '七', '八', '九'];
    var arr2 = ['', '十', '百', '千', '万', '十', '百', '千', '亿', '十', '百', '千', '万', '十', '百', '千', '亿'];//可继续追加更高位转换值
    if (!num || isNaN(num)) {
      return "零";
    }
    var english = num.toString().split("");

    var result = "";
    for (var i = 0; i < english.length; i++) {
      var des_i = english.length - 1 - i; //倒序排列设值
      result = arr2[i] + result;
      var arr1_index = english[des_i];
      result = arr1[+arr1_index] + result;
    }
    //将【零千、零百】换成【零】 【十零】换成【十】
    result = result.replace(getRegExp('零(千|百|十)', 'g'), '零').replace(getRegExp('十零', 'g'), '十');
    //合并中间多个零为一个零
    result = result.replace(getRegExp('零+', 'g'), '零');
    //将【零亿】换成【亿】【零万】换成【万】
    result = result.replace(getRegExp('零亿', 'g'), '亿').replace(getRegExp('零万', 'g'), '万');
    //将【亿万】换成【亿】
    result = result.replace(getRegExp('亿万', 'g'), '亿');
    //移除末尾的零
    result = result.replace(getRegExp('零+$'), '');
    //将【零一十】换成【零十】
    //result = result.replace(/零一十/g, '零十');//貌似正规读法是零一十
    //将【一十】换成【十】
    result = result.replace(getRegExp('^一十', 'g'), '十');
    return result;
  }
}

  module.exports = {
    keepDecimal: filters.keepDecimal,
    convertToChinaNum: filters.convertToChinaNum,
  }
```

## 工具函数

```js
import { ROLE_NAME } from './config.js';
/** 
 * 时间戳转化为年 月 日 时 分 秒 
 * number: 传入时间戳 
 * format：返回格式，支持自定义，但参数必须与formateArr里保持一致 
 * ( 
 * 转换案例:
 * console.log(time.formatTime(sjc,'Y/M/D h:m:s'));
 * console.log(time.formatTime(sjc, 'h:m'));
 * )
 */
function formatTimeTwo(number, format) {
  if(!number) return
  var formateArr = ['Y', 'M', 'D', 'h', 'm', 's'];
  var returnArr = [];
  var date = new Date(number * 1000);
  returnArr.push(date.getFullYear());
  returnArr.push(formatNumber(date.getMonth() + 1));
  returnArr.push(formatNumber(date.getDate()));
  returnArr.push(formatNumber(date.getHours()));
  returnArr.push(formatNumber(date.getMinutes()));
  returnArr.push(formatNumber(date.getSeconds()));
  for (var i in returnArr) {
    format = format.replace(formateArr[i], returnArr[i]);
  }
  return format;
}

/**
 * @description 通过时间戳获取到当前周几
 * @param {number} number 时间戳
 * @returns 
 */
function getWeek(number) {
  if (!number) return
  number = parseInt(number)
  if (`${number}` <= 10) number = number * 1000
  const week = new Date(number * 1000).getDay()
  const weekArr = [ '日', '一', '二', '三', '四', '五', '六']
  return weekArr[week]
}

// 日期转时间戳
function formatTime(date) {
  var year = date.getFullYear()
  var month = date.getMonth() + 1
  var day = date.getDate()
  var hour = date.getHours()
  var minute = date.getMinutes()
  var second = date.getSeconds()
  return [year, month, day].map(formatNumber).join('/') + ' ' + [hour, minute, second].map(formatNumber).join(':')
}

function formatNumber(n) {
  n = n.toString()
  return n[1] ? n : '0' + n
}

//  年月日
const formatTimeYMD = date => {
  const year = date.getFullYear()
  const month = date.getMonth() + 1
  const day = date.getDate()
  return [year, month, day].map(formatNumber).join('-')
}

// 获取最近几天时间（-月-日）
const getDay = (day, type, optionDate = '') => {
  var today
  if (optionDate) {
    today = new Date(optionDate);
  } else {
    today = new Date();
  }
  var targetday_milliseconds = today.getTime() + 1000 * 60 * 60 * 24 * day;
  today.setTime(targetday_milliseconds); //注意，这行是关键代码
  var tMonth = today.getMonth();
  var tDate = today.getDate();
  tMonth = doHandleMonth(tMonth + 1);
  tDate = doHandleMonth(tDate);
  if (type == 0) {
    return tMonth + "月" + tDate + "日"
  } else if (type == 1) {
    return tMonth + "." + tDate;
  } else if (type == 3) {
    //  预约列表使用，返回01.01 和当日零点时间戳
    return { text: tMonth + "." + tDate, time: new Date(targetday_milliseconds).setHours(0, 0, 0, 0) / 1000 }
  }
}

const getYears = (day) => {
  var today = new Date();
  var targetday_milliseconds = today.getTime() + 1000 * 60 * 60 * 24 * day;
  today.setTime(targetday_milliseconds); //注意，这行是关键代码
  var tYear = today.getFullYear();
  var tMonth = today.getMonth();

  tMonth = doHandleMonth(tMonth + 1);

  return tYear + "年" + tMonth + "月";
}

const doHandleMonth = month => {
  var m = month;
  if (month.toString().length == 1) {
    m = "0" + month;
  }
  return m;
}

// 01月08日 10:30 转时间戳 1546914600 老客预约时调用
const getMDHMToTimes = (date, hours, minutes) => {
  // 清空年、日字符串 结果01,01
  const replace_date = date.replace('月', ',').replace('日', ',')
  // 分割 01,01 为数组 [01,01]
  const [this_month, this_date] = replace_date.split(',')
  let year = new Date().getFullYear();

  // 当月是否为12月且为最后一天31, 且传入的月份小于12 则年份 + 1
  const _month = new Date().getMonth() + 1 
  const _date = new Date().getDate()
  if (_month == 12 && _date == 31 && this_month < _month) {
    year += 1
  }

  const format_date = `${year}-${this_month}-${this_date}`

  let time = new Date(format_date);
  time.setHours(hours, minutes, 0, 0);
  let timestamp = new Date(time).getTime() / 1000;
  return timestamp;
}
// 弹窗
const Message = {
  modal(content, options = {}) {
    return new Promise((resolve, reject) => {
      let defaults = {
        title: '提示',
        confirmText: '确定',
        cancelText: '取消'
      }
      Object.assign(defaults, options);
      wx.showModal({
        // title: '提示',
        ...defaults,
        content,
        success(res) {
          if (res.confirm) {
            resolve(true);
          } else if (res.cancel) {
            resolve(false);
          }
        }
      })
    });
  },
  warn(content, options = {}) {
    let defaults = {
      title: '提示',
      confirmText: '确定'
    }
    Object.assign(defaults, options);
    return new Promise((resolve, reject) => {
      wx.showModal({
        ...defaults,
        content: content,
        showCancel: false,
        success() {
          resolve()
        }
      });
    });
  },
  warns(title) {
    wx.showToast({
      title,
      icon: 'none'
    })
  },
  success(title) {
    wx.showToast({
      title,
      icon: 'success'
    })
  },
  info(title) {
    wx.showToast({
      title,
      icon: 'loading',
      duration: 1000
    })
  },
  loading: {
    show(title) {
      wx.showLoading({
        mask: true,
        title,
      })
    },
    hide() {
      wx.hideLoading();
    }
  }
}

// 距离 1000m => 1km
const formatDistance = distance => {
  if (distance >= 1000) {
    return (distance / 1000).toFixed(2) + 'km'
  } else {
    return distance + 'm'
  }
}

// 电话号码验证
const validPhone = (phone) => {
  return (/^1[3456789]\d{9}$/.test(phone))
}

//数字保留一位小数和小数点后两位为0则截取
const keepDecimal = (money) => {
  let rescast = money.toString();
  let obj = rescast.split('.');

  // 保留两位小数
  if (obj.length == 2 && obj[1] != '00') {
    return Number(rescast).toFixed(2) * 1;
  }
  // 保留整数
  if (obj.length == 2 && obj[1] == '00') {
    return Number(rescast).toFixed(0) * 1;
  }

  return Number(rescast);
}
// 全局金额四舍五入
const mathRound = (num) => {
  return Math.round(num);
}

//回到顶部
const goTop = () => { // 一键回到顶部
  if (wx.pageScrollTo) {
    wx.pageScrollTo({
      scrollTop: 0
    })
  } else {
    wx.showModal({
      title: '提示',
      content: '当前微信版本过低，无法使用该功能，请升级到最新微信版本后重试。'
    })
  }
}
/**
 * 时间顶部tab滑块
 * 返回昨日，今日，本周，本旬，本月，开始与结束时间戳
 */
const getDateNavsTabTimestamp = {
  // 初始化实例
  getDates() {
    let _Date = new Date();
    // 获取年份
    let _Year = _Date.getFullYear();
    // 获取月份
    let _Month = _Date.getMonth();
    // 获取今天本周的第几天 返回值是 0（周日） 到 6（周六） 之间的一个整数。0||7为7，即weekday的值为1-7
    let _Day = _Date.getDay() || 7;
    // 获取今天日期
    let _Today = _Date.getDate();
    return { _Date, _Year, _Month, _Day, _Today };
  },
  // 返回今日开始-结束时间戳
  getTodayStartEndTimestamp() {
    let { _Date } = this.getDates();
    // 今日0点
    let start = _Date.setHours(0, 0, 0, 0) / 1000;
    // 今日23:59:59
    let end = _Date.setHours(23, 59, 59, 0) / 1000;
    return { start_time: start, end_time: end }
  },
  // 返回明日开始-结束时间戳
  getTomorrowStartEndTimestamp() {
    let { _Date } = this.getDates();
    // 明日0点
    let start = _Date.setHours(+24, 0, 0, 0) / 1000;
    // 明日23:59:59
    let end = _Date.setHours(23, 59, 59, 0) / 1000;
    return { start_time: start, end_time: end }
  },
  // 返回昨日开始-结束时间戳
  getYesterdayStartEndTimestamp() {
    let { _Date } = this.getDates();
    let start = _Date.setHours(-24, 0, 0, 0) / 1000;
    let end = _Date.setHours(23, 59, 59, 0) / 1000;
    return { start_time: start, end_time: end }
  },
  // 返回本周开始-结束时间戳
  getWeekStartEndTimestamp() {
    let { _Date, _Day, _Today } = this.getDates();
    // 本周第一天0点
    let num = _Today - _Day + 1; //往前算（weekday-1）天，年份、月份会自动变化
    _Date.setDate(num);
    let start = _Date.setHours(0, 0, 0, 0) / 1000;
    // 本周最后一天23:59:59
    _Date.setDate(_Date.getDate() + 6);
    let end = _Date.setHours(23, 59, 59, 0) / 1000;
    return { start_time: start, end_time: end };
  },
  // 本月开始-结束时间时间戳
  getMonthStartEndTimestamp() {
    const { _Year, _Month } = this.getDates()
    // 本月第一天0点
    const start = new Date(_Year, _Month, 1).getTime() / 1000
    // 本月最后一天23:59:59
    const end = new Date(_Year, _Month + 1, 0).setHours(23, 59, 59, 0) / 1000

    return { start_time: start, end_time: end }
  },
  // 本旬开始-结束时间时间戳
  getQuarterStartEndTimestamp() {
    let { _Year, _Month, _Today } = this.getDates();
    let monthStartDate = new Date(_Year, _Month, 1);
    let monthEndDate = new Date(_Year, _Month + 1, 1);
    // // 本月最后一天日期
    let end_day = (monthEndDate - monthStartDate) / (1000 * 60 * 60 * 24);
    let start;
    let end;
    if (1 <= _Today && _Today <= 10) {
      // 上旬第一天0点
      start = new Date(_Year, _Month, 1).getTime() / 1000;
      // 上旬最后一天23:59:59
      end = new Date(_Year, _Month, 10).setHours(23, 59, 59, 0) / 1000;
      // console.log('上旬');
    } else if (11 <= _Today && _Today <= 20) {
      // 中旬开始-结束时间时间戳
      start = new Date(_Year, _Month, 11).getTime() / 1000;
      end = new Date(_Year, _Month, 20).setHours(23, 59, 59, 0) / 1000;
      // console.log('中旬');
    } else if (21 <= _Today && _Today <= end_day) {
      // 下旬开始-结束时间时间戳
      start = new Date(_Year, _Month, 21).getTime() / 1000;
      end = new Date(_Year, _Month, end_day).setHours(23, 59, 59, 0) / 1000;
      // console.log('下旬');
    }
    return { start_time: start, end_time: end };
  },
  // 本年开始-结束时间时间戳
  getYearStartEndTimestamp() {
    const { _Year } = this.getDates()
    // 本年第一天0点
    const start = new Date(_Year, 0, 1).getTime() / 1000
    // 本年最后一天23:59:59
    const end = new Date(_Year, 12, 0).setHours(23, 59, 59, 0) / 1000

    return { start_time: start, end_time: end }
  },
  // 全部开始-结束时间时间戳
  getAllStartEndTimestamp(){
    let { _Date } = this.getDates();
    // 今日0点
    let start = 1;
    // 今日当前时间戳
    let end = parseInt(_Date.getTime() / 1000);
    return { start_time: start, end_time: end }
  }
}
/**
 * 计算距离生日还差多少天
 * @param time 时间戳
 */
function getDaysToBirthday(time) {
  if (!time) { return '' }
  
  // 获取生日 月份、日期
  const month = new Date(time * 1000).getMonth()
  const day = new Date(time * 1000).getDate()

  // 获取今天日期，设置时间为 0 时 0 分 0秒
  const nowTime = new Date(new Date().setHours(0, 0, 0, 0));
  const thisYear = nowTime.getFullYear();
  // 今年的生日 统一时间时分秒
  const birthday = new Date(new Date(thisYear, month, day).setHours(0, 0, 0, 0));

  //今年生日已过，则计算距离明年生日的天数
  if (birthday < nowTime) {
      birthday.setFullYear(nowTime.getFullYear() + 1);
  }
  const timeDec = birthday - nowTime;
  const days = timeDec / (24 * 60 * 60 * 1000);
  
  return Math.ceil(days);
}

/**获取swiper列表高度
 * that: 当前页面栈this
 * idName：id名称
 * author:jayhao
 * date:2019-10-10
 */
function getSwiperListHeight(that,idName) {
  let query = wx.createSelectorQuery();
  query.select(idName).boundingClientRect()
  query.exec(res => {
    that.setData({
      list_content_height: `${Math.floor(res[0].height)}`
    })
  });
}
/**检查分页页数
 * page: 当前页
 * pagesize: 每页条数
 * total：总页数
 * author:jayhao
 * date:2019-10-10
 */
function checkPageNum(page, pagesize, total) {
  // 计算页数
  total = total ? total : 1
  let totalPage = Math.ceil(total / pagesize);
  // 页数是否大于总页数
  if (page > totalPage && totalPage > 0) {
    Message.warns('暂无更多数据')
    return true;
  } else {
    return false;
  }
}


/**
 * 获取系统信息 
 * that: 传入当前页面栈 this
 * author:jayhao
 * date:2019-10-9
 */
function getAppSystemInfo(that) {
  return new Promise(resolve => {
    wx.getSystemInfo({
      success: (res) => {
        if (that) {
          that.setData({
            windowHeight: res.windowHeight,
            windowWidth: res.windowWidth,
          })
        }
        resolve(res)
      },
    })
  })
}

/**
 * 自定义底部菜单跳转信息
 * this: 当前页面栈this
 * index：菜单下标 
 * author:jayhao
 * date:2019-10-11
 */
function SwitchTabMenu(that, index) {
  if (typeof that.getTabBar === 'function' && that.getTabBar()) {
    that.getTabBar().setData({ selected: index })
  }
}

// 角色名称
function FilterRoleName(name) {
  // 判断对象是否含有指定的属性
  if (ROLE_NAME.hasOwnProperty(name)) {
    return ROLE_NAME[name].name
  } else {
    // 兼容传进来是角色id
    var role_name = '职位不详'
    for (let key in ROLE_NAME) {
      const roule_id = Object.getOwnPropertyDescriptor(ROLE_NAME[key], 'id').value
      if (Number(roule_id) === name) {
        return ROLE_NAME[key].name
      }
    }
    return role_name
  }
}

/**同步获取本地存储
 * @params key key name
 */
function getStorageData(key) {
  return new Promise(resolve => {
    wx.getStorage({
      key,
      success(res) {
        resolve(res.data)
      },
      fail(err) {
        resolve('')
      }
    })
  })
}
/**验证模块权限
 * @param {Array} value 权限数组
 * @return Boolean
 * @author jayhao
 * @date 2019-11-12
 */
function CheckPermission(value) {
  if (value && value instanceof Array && value.length > 0 && wx.getStorageSync('access_token')) {
    const permission = value
    const user_permissions = wx.getStorageSync('user_permissions')

    if (user_permissions){

      const hasPermission = user_permissions.some(per => {
        return permission.includes(per)
      })

      if (!hasPermission) {
        return false
      }
      return true
      
    }  else {
      return false
    }

  } else {
    console.error(`need permission! Like checkPermission(['api/x1/x1','api/x2/x2'])`)
    return false
  }
}

/**验证角色是否为区域
 * @params role_name 角色名称
 * @author jayhao
 * @date 2019-11-14
 */
function CheckManager(role_name){
  return ['area_manager', 'sub_manager', 'other'].includes(role_name)
}

/**
 * 身份证验证
 * @param idCard string
 * @return {boolean}
 * @author jayhao
 * @date 2020-9-9
 */
function checkIdCard(idCard) {
  if (typeof idCard !== 'string') {
    throw new Error('ID card need string type')
  }
  // 加权因子
  const weight_factor = [7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2]
  // 校验码
  const check_code = ['1', '0', 'X', '9', '8', '7', '6', '5', '4', '3', '2']
  // 最后一位
  const last = idCard[17]
  const seventeen = idCard.substring(0, 17)
  // ISO 7064:1983.MOD 11-2
  // 判断最后一位校验码是否正确
  const arr = seventeen.split('')
  const len = arr.length
  let num = 0
  for (let i = 0; i < len; i++) {
    num = num + arr[i] * weight_factor[i]
  }
  // 获取余数
  const resisue = num % 11
  const last_no = check_code[resisue]
  // 格式的正则、正则思路
  /*
  第一位不可能是0
  第二位到第六位可以是0-9
  第七位到第十位是年份，所以七八位为19或者20
  十一位和十二位是月份，这两位是01-12之间的数值
  十三位和十四位是日期，是从01-31之间的数值
  十五，十六，十七都是数字0-9
  十八位可能是数字0-9，也可能是X
  */
  const idcard_patter = /^[1-9][0-9]{5}([1][9][0-9]{2}|[2][0][0|1][0-9])([0][1-9]|[1][0|1|2])([0][1-9]|[1|2][0-9]|[3][0|1])[0-9]{3}([0-9]|[X])$/
  // 判断格式是否正确
  const format = idcard_patter.test(idCard)
  // 返回验证结果，校验码和格式同时正确才算是合法的身份证号码
  return last === last_no && format
}


/**
   * @description: 货币转换 （将金额转为中文大写）
   * @author kyun
   * @date 2021/3/25
  */
function convertCurrency(money) {
  if (money === '') { return '' }
  money = parseFloat(money)
  if (!money && money !== 0) {
    // eslint-disable-next-line no-throw-literal
    throw { code: -1, msg: '金额格式错误' }
  }
  // 汉字的数字
  const cnNums = ['零', '壹', '贰', '叁', '肆', '伍', '陆', '柒', '捌', '玖']
  // 基本单位
  const cnIntRadice = ['', '拾', '佰', '仟']
  // 对应整数部分扩展单位
  const cnIntUnits = ['', '万', '亿', '兆']
  // 对应小数部分单位
  const cnDecUnits = ['角', '分', '毫', '厘']
  // 整数金额时后面跟的字符
  const cnInteger = '整'
  // 整型完以后的单位
  const cnIntLast = '元'
  // 最大处理的数字
  const maxNum = 999999999999999.9999
  // 金额整数部分
  let integerNum
  // 金额小数部分
  let decimalNum
  // 输出的中文金额字符串
  let chineseStr = ''
  // 分离金额后用的数组，预定义
  let parts
  if (money >= maxNum) {
    // eslint-disable-next-line no-throw-literal
    throw { code: -1, msg: '超出最大处理数字' }
  }
  if (money === 0) {
    chineseStr = cnNums[0] + cnIntLast + cnInteger
    return chineseStr
  }
  // 转换为字符串
  money = money.toString()
  if (money.indexOf('.') === -1) {
    integerNum = money
    decimalNum = ''
  } else {
    parts = money.split('.')
    integerNum = parts[0]
    decimalNum = parts[1].substr(0, 4)
  }
  // 获取整型部分转换
  if (parseInt(integerNum, 10) > 0) {
    let zeroCount = 0
    const IntLen = integerNum.length
    for (let i = 0; i < IntLen; i++) {
      const n = integerNum.substr(i, 1)
      const p = IntLen - i - 1
      const q = p / 4
      const m = p % 4
      if (n === '0') {
        zeroCount++
      } else {
        if (zeroCount > 0) {
          chineseStr += cnNums[0]
        }
        // 归零
        zeroCount = 0
        chineseStr += cnNums[parseInt(n)] + cnIntRadice[m]
      }
      if (m === 0 && zeroCount < 4) {
        chineseStr += cnIntUnits[q]
      }
    }
    chineseStr += cnIntLast
  }
  // 小数部分
  if (decimalNum !== '') {
    const decLen = decimalNum.length
    for (let i = 0; i < decLen; i++) {
      const n = decimalNum.substr(i, 1)
      if (n !== '0') {
        chineseStr += cnNums[Number(n)] + cnDecUnits[i]
      }
    }
  }
  if (chineseStr === '') {
    chineseStr += cnNums[0] + cnIntLast + cnInteger
  } else if (decimalNum === '') {
    chineseStr += cnInteger
  }
  return chineseStr
}

/**
 * 订阅消息
 * @param {Array} tmplIds 消息模板的ids
 */
function requestSubscribeMsg(tmplIds) {
  return new Promise((resolve, reject) => {
   wx.requestSubscribeMessage({
     tmplIds,
     success(res) {
       resolve(res)
     },
     fail(e) {
       if (e.errCode == 20004) {
         wx.showModal({
           title: '提示',
           content: '通知已关闭，请前往设置开启通知',
           showCancel: false,
           confirmText: '知道了'
         });
       }
       reject(e)
     }
    })
  })
}

// 补0
function zero(num) {
  if (!Number(num)) return 0
  if (Number(num) < 10) {
    return `0${Number(num)}`
  } else {
    return Number(num)
  }
}

// 上报we分析
function reportEventFn(report_key, params = {}) {
  const { position_name, stores = [{}], name, nickname } = wx.getStorageSync('sys_user_info') || {}
  wx.reportEvent(report_key, {
    "name": name || '',
    "store": stores[0] ? stores[0].name : '',
    "nickname": nickname || '',
    "city": stores[0] ? stores[0].province_name + '-' + stores[0].city_name : '',
    "position": position_name || '',
    ...params
  })
}

/**
 * 使用test方法实现模糊查询
 * @param  {Array}  list     原数组
 * @param  {String} keyWord  查询的关键词
 * @return {Array}           查询的结果
*/
function fuzzyQuery(list, keyWord, key) {
  var reg =  new RegExp(keyWord);
  var arr = [];
  for (var i = 0; i < list.length; i++) {
    if (reg.test(list[i][key])) {
      arr.push(list[i]);
    }
  }
  return arr;
}

module.exports = {
  formatTime,
  getWeek,
  formatTimeYMD,
  formatTimeTwo,
  Message,
  getDay,
  validPhone,
  getYears,
  getMDHMToTimes,
  getDateNavsTabTimestamp,
  goTop,
  keepDecimal,
  mathRound,
  formatDistance,
  getSwiperListHeight,
  checkPageNum,
  getAppSystemInfo,
  SwitchTabMenu,
  FilterRoleName,
  getStorageData,
  CheckPermission,
  CheckManager,
  checkIdCard,
  getDaysToBirthday,
  convertCurrency,
  requestSubscribeMsg,
  zero,
  reportEventFn,
  fuzzyQuery
}
```

## app.js

```js
//app.js
import { OSS_URL, OSS_IMG_URL, SUCCESS_CODE, ENV, TENCENT_MAP_KEY } from './utils/config.js';
import { getSystemUserLogin, getSystemUserCommon, checkApplicationAccount, getUserPermissions, getSysUserInfo } from './api/user.js';
import { updateMsgRead } from './api/common.js'
import QQMapWX from './utils/maps/qqmap-wx-jssdk.min.js';
import { Message, getDateNavsTabTimestamp, getStorageData, CheckPermission, getAppSystemInfo } from './utils/util.js';
// 阿拉丁小程序数据上报
// const ald = require('./utils/ald-stat.js')
App({
  ENV,
  oss_url: OSS_URL,
  oss_img_url: OSS_IMG_URL,
  yesterdayTime: getDateNavsTabTimestamp.getYesterdayStartEndTimestamp(),
  todayTime: getDateNavsTabTimestamp.getTodayStartEndTimestamp(),
  weekTime: getDateNavsTabTimestamp.getWeekStartEndTimestamp(),
  quarterTime: getDateNavsTabTimestamp.getQuarterStartEndTimestamp(),
  monthTime: getDateNavsTabTimestamp.getMonthStartEndTimestamp(),
  yearTime: getDateNavsTabTimestamp.getYearStartEndTimestamp(),
  allTime: getDateNavsTabTimestamp.getAllStartEndTimestamp(),
  globalData: {
    userInfo: null,
    // 是否iPhone X 以上型号
    isIphoneX: false,
    // 是否冻结
    freezeInfo: {
      freeze: false,
      info: {}
    },
    // 是否体验积分模块
    isPointExperience: false
  },
  // 默认本地存储门店ids key
  default_ids: 'default_ids',
  echarPicColor: [
    '#37A2DA', '#32C5E9', '#9FE6B8', '#FFDB5C', '#FF9F7F', '#FB7293', '#E7BCF3', '#8378EA',
    '#95A3FF', '#88DDFF', '#CB97FF', '#F99690', '#66BB6A', '#FF8F00',
    '#DD4C51', '#F7A128', '#3AA255', '#E65832', '#76C0CB', '#CC3D41', '#4EB849', '#E0719C',
    '#D45A59', '#F89A80', '#F37674', '#039FB8', '#F8D2D6', '#D0545F', '#975E6D', '#DD4C51',
    '#C94A44', '#FB7293', '#FF9F7F', '#FFDB5C', '#32C5E9', '#37A2DA', '#AEFDCA', '#E062AE', '#8378EA',
  ],
  onLaunch() {
    this.getSystemInfo()
  },
  async onShow(o) {
    // 更新版本
    this.updateManager()
    // 获取场景值及参数
    const { scene, query, path } = wx.getLaunchOptionsSync()
    const access_token = wx.getStorageSync('access_token')
    const is_phone = wx.getStorageSync('is_phone')
    const is_bind = wx.getStorageSync('is_bind')

    // 免密登录跳指定页
    // 使用场景：
    // 1、会议邀请、
    // 2、分享顾客档案 直条 蕾粉+
    const isUserLogin = await new Promise(resolve => {
      // 是否含有免密登录标识 is_login
      if (query.hasOwnProperty('is_login')) {
        const isLogin = Number(query.is_login)
        if (isLogin === 0) {
          resolve(0)
        } else {
          resolve(1)
        }
      } else {
        resolve(1)
      }
    })
    // 免密登录则不执行以下验证逻辑
    if (!isUserLogin) { return }


    // 且目标非登录页
    if (path !== 'pages/login/login' && !access_token) {
      
      // 判断是否有携带 msg_id, 则发起请求更新消息为已读
      if (query.hasOwnProperty('msg_id')) {
        this.updateMsgRead({ msg_id: query.msg_id })
      }

      // 分享-单人聊天卡片、群卡片进去
      if (scene == 1007 || scene == 1008) {
        const pages = this.getQueryFormat(path, query)
        wx.setStorageSync('share_pages', pages)
      }
     
      // 公众号模板消息，扫描二维码
      // 新客到店、生日提醒
      if ([1043, 1011].includes(scene)) {
        const pages = this.getQueryFormat(path, query)
        wx.setStorageSync('pages', pages)
      }
      
      this.goLogin()

    } else if (path == 'pages/login/login') {
      // 分享-邀请注册会进去这里
      if (scene == 1007 || scene == 1008) {
        const pages = this.getQueryFormat(path, query)
        wx.setStorageSync('share_data', query)
        wx.setStorageSync('share_pages', pages)
      }
      // 判断是否有携带 msg_id
      if (query.hasOwnProperty('msg_id')) {
        this.updateMsgRead({ msg_id: query.msg_id })
      }
    }

    // 有token 才去检查账号审批状态 且已绑定手机 且 未绑定账号 有检查是否有账号申请
    if (access_token && is_phone === 1 && is_bind === 0) {
      this.checkApplicationAccount()
    }


    // 有门店分组权限-非区域首页时，切换后台重新显示则检查是否已超过10分钟（无用业务、暂时不删除）
    // if (wx.getStorageSync('is_multiple_store')) {
    //   const this_timestamp = Date.parse(new Date()) / 1000
    //   // 检查expired_time是否过期
    //   const expired_time = wx.getStorageSync('expired_time')
    //   if (this_timestamp > expired_time && expired_time > 0) {
    //     // 清除分组信息
    //     wx.setStorage({ key: 'expired_time', data: 0, })
    //     wx.setStorage({ key: 'default_ids', data: [], })
    //     wx.setStorage({ key: 'group_names', data: '', })
    //   }
    // }

  },
  onHide() {
    wx.removeStorageSync('share_pages')
    wx.removeStorageSync('is_login')
  },

  // 获取文件目录，避免../../情况，基于app.js引入
  // 通过此方法不能引入分包的内容，只能引入主包的，因为app.js只存在主包
  optimizeRequire(path) {
    if(path.includes('package_one')) {
      Message.warn('来自app.js的optimizeRequire方法报错，不能引入分包的内容')
    } else {
      return require(`./${path}`)
    }
  },

  // 获取系统信息
  async getSystemInfo() {
    const info = await getAppSystemInfo()
    
    // 具体问题 查看微信开发者社区
    // https://developers.weixin.qq.com/community/develop/doc/000488ef5e06880968872dc9658400?highLine=iPhone%2520%25E5%25BA%2595%25E9%2583%25A8%25E5%25AE%2589%25E5%2585%25A8%25E5%259F%259F
    // 获取客户端平台
    const isIos = info.platform.toLowerCase() == 'ios'
    // 适配 iPhone 底部安全区
    if (isIos) {
      // 获取设备状态栏
      const statusBarHeight = info.statusBarHeight
      // 是否 iPhone X 以上设备，ios设备状态栏 大于 44, 满足以上两条件
      // 因为 iPhone X 以上 状态栏 都为 44
      this.globalData.isIphoneX = (isIos && statusBarHeight >= 44)
    }
  },

  // 版本更新方法
  async updateManager() {
    if (wx.canIUse('getUpdateManager')) {
      const updateManager = wx.getUpdateManager()
      updateManager.onCheckForUpdate(function(res) {
        if (res.hasUpdate) {
          updateManager.onUpdateReady(function() {
            updateManager.applyUpdate()
          })
          updateManager.onUpdateFailed(function() {
            Message.modal('新版本已经上线啦~，请您删除当前小程序，重新搜索打开哟~', { title: '更新失败' })
          })
        }
      })
    } else {
      Message.modal('当前微信版本过低，无法使用该功能，请升级到最新微信版本后重试')
    }
  },
  // 检验当前微信用户的session_key是否有效
  checkWxUsaerSession() {
    return new Promise((resolve, reject) => {
      wx.checkSession({
        success(res) {
          //处于登录态;
          resolve(true);
          console.log('登录态==', res)
        },
        fail(err) {
          //需要重新登录;
          resolve(false);
          console.log('登录态==', err)
        }
      })
    });
  },

  // 微信用户登录
  getWxLogin() {
    console.log('微信用户登录获取code')
    return new Promise((resolve, reject) => {
      wx.login({
        success(res) {
          console.log('code===', res)
          resolve(res.code);
        }
      })
    });
  },

  // 获取用户信息
  // getWxUserInfo() {
  //   let _this = this;
  //   return new Promise((resolve, reject) => {
  //     if (_this.globalData.userInfo) {
  //       resolve(_this.globalData.userInfo);
  //     } else {
  //       wx.getSetting({
  //         success(res) {
  //           console.log('reee', res)
  //           console.log('检查用户是否授权')
  //           if (res.authSetting['scope.userInfo']) {
  //             console.log('----用户已授权----')
  //             // 已经授权，可以直接调用 getUserInfo 获取头像昵称
  //             wx.getUserInfo({
  //               success(res) {
  //                 _this.globalData.userInfo = res;
  //                 resolve(res);
  //               },
  //               fail(e) {
  //                 reject(e);
  //               }
  //             })
  //           } else {
  //             reject(res);
  //             // Message.modal('只有授权后，才可以使用小程序哦~')
  //           }
  //         }
  //       });
  //     }
  //   });
  // },
  // 获取用户信息
  getWxUserInfo() {
    return new Promise((resolve, reject) => {
      if (this.globalData.userInfo) {
        resolve(this.globalData.userInfo)
      }
      reject(this.globalData.userInfo)
    });
  },

  // 智慧管店触发登录
  async adminToLoing() {
    console.log("智慧管店登录");
    try {
      // 获取 WX code
      const code = await this.getWxLogin();
      // 获取微信用户信息
      const info = await this.getWxUserInfo();
      // 发起后端登录
      const loginStatus = await this.adminUserLogin(code, info);
        // 返回用户绑定状态
      if (loginStatus) {
        const { is_phone, is_bind } = loginStatus
        return { is_phone, is_bind };
      }
      return loginStatus
    } catch (e) {
      console.log('e', e);
    }
  },

  // 智慧管店用户登录
  async adminUserLogin(code, info) {
    try {
      console.log("智慧管店用户登录");
      let params = {
        code: code,
        rawData: info.rawData,
        signature: info.signature,
        encryptedData: info.encryptedData,
        iv: info.iv,
      };
      const res = await getSystemUserLogin(params);
      if (res.code == SUCCESS_CODE || res.code == 1005) {
        const { access_token, checkbindphone: is_phone, checkbinduser: is_bind, open_id: user_open_id } = res.data
        // 存储用户状态 保存token
        wx.setStorageSync("access_token", access_token);
        wx.setStorageSync('is_phone', is_phone);
        wx.setStorageSync('is_bind', is_bind);
        wx.setStorageSync('user_open_id', user_open_id);
        return { is_phone, is_bind  };
      } else {
        Message.warn(res.msg)
        // 不管返回什么错直接清除数据
        wx.clearStorageSync()
        wx.clearStorage()
        return false;
      }
    } catch (e) {
      console.log('adminUserLogin-error', e)
      //  账号长时间不登陆给冻结了
      if (e.code === 4024) {
        this.globalData.freezeInfo = { freeze: true, info: e.data.apply, id: e.data.id, nickname: e.data.nickname }
      }
      return false;
    }
  },

  // 获取智慧管店用户状态
  async getSystemUserCommon() {
    try {
      // 是否有注册状态、有则不显示 '账号尚未绑定' 提示，避免用户困扰，否则反之
      const register_status =  wx.getStorageSync('isRegister') || false

      const res = await getSystemUserCommon({}, { needMsg: !register_status });
      const { checkbindphone: is_phone, checkbinduser: is_bind } = res.data 
      wx.setStorageSync('is_phone', is_phone);
      wx.setStorageSync('is_bind', is_bind);
      // 返回状态
      return { is_phone, is_bind };
    } catch (e) {
      console.log(e);
    }
  },

  // 检查是否有账号申请
  async checkApplicationAccount() {
    const res = await checkApplicationAccount()
    // 账号审批状态 -1：审核不通过、0审核中、1审核通过
    const { form_id, company_name, status, token } = res.data
    // 没拒绝。没通过、审核记录直接删除。则去绑定页
    if (typeof status === 'object') {
      this.goBinding()
    } else if (status === 1){
      // 审核通过覆盖本地 access_token
      wx.setStorageSync('access_token', token.access_token)
      this.goLogin()
    } else {
      wx.reLaunch({
        url: `/pages/success/success?status=${status}&company=${company_name}&form_id=${form_id}`,
      })
    }
  },
  // 标识公众号模板消息已读
  async updateMsgRead(params) {
    await updateMsgRead(params, { needLoading: false, needMsg: false })
  },
  // 获取用户权限
  async getUserPermissions() {
    const res = await getUserPermissions({}, { needLoading: false })
    // 保存权限到本地
    wx.setStorageSync('user_permissions', res.data)
  },
  // 判断跳转对应路由
  async getRouting() {
    let url = '/pages/index/index';
    // 分享一周报表数据跳转
    const share_pages = wx.getStorageSync('share_pages')
    // 公众号模板消息
    const pages = wx.getStorageSync('pages')

    // 跳转路由前，判断本地是否有 权限池，且有登录 token，则请求个人权限 （此处主要是处理没有登录，然后直接跳转路由逻辑）
    if (await getStorageData('user_permissions') == '' && await getStorageData('access_token')) {
      await this.getUserPermissions()
    }

    if (pages) {
      url = pages
    }
    if (share_pages) {
      url = share_pages
    }
    wx.reLaunch({
      url: url,
      complete(c) {
        wx.removeStorageSync('pages')
        wx.removeStorageSync('share_pages')
      }
    })
  },

  // 获取地理位置经纬度
  getLocation() {
    return new Promise((resolve, reject) => {
      wx.getLocation({
        type: 'gcj02',
        success: function (res) {
          let qqmapsdk = new QQMapWX({
            key: TENCENT_MAP_KEY
          })
          // 调用接口
          qqmapsdk.reverseGeocoder({
            location: {
              latitude: res.latitude,
              longitude: res.longitude
            },
            coord_type: 5,
            success: r => {
              const  { address } = r.result
              resolve({
                address,
                latitude: res.latitude,
                longitude: res.longitude
              })
            },
            fail: err => {
              reject(false)
            }
          })
        },
        fail(e) {
          reject(e)
        }
      })
    });
  },
  /**用户功能模块权限判断及设置
   * @that 当前page this
   * @user_permissions 权限对象
   * @demo { hasCustomerImage: CheckPermission(['api/customer/images']) }
   */
  setUserPermissions(that, module_permissions) {
    // 统一使用 'module_permissions' 变量名，不可更改！！！！
    for (let key in module_permissions) {
      module_permissions[key] = CheckPermission(module_permissions[key])
    } 
    that.setData({ module_permissions })
  },

  /**=======================多选门店 start========================== */

  // 获取是否有多选门店及显示门店icon 权限
  async IsMultipleStore() {
    // return wx.getStorageSync('is_multiple_store')
    const is_multiple_store = await new Promise(resolve => {
      wx.getStorage({
        key: 'is_multiple_store',
        success: function (res) {
          resolve(res.data)
        },
        fail(err) {
          resolve(false)
        }
      })
    })
    return is_multiple_store
  },

  // 多门店选择路由跳转
  selectStore(storeOption) {
    wx.navigateTo({
      url: "/pages/multistoreselect/multistoreselect",
    })
  },

  // 门店分组-初始化区域离开小程序过期时间
  // initExpiredTime() {
  //   wx.setStorage({
  //     key: 'expired_time',
  //     data: -1,
  //   })
  // },

  /**检查新门店ids 与旧门店ids 是否一致，如为一致则不加载数据，否则反之
   * @params new_ids 新增门店ids
   * @params old_ids 旧门店ids
   */
  CheckTheSameStoreIds(new_ids, old_ids) {
    const new_store = new_ids
    const old_store = old_ids || []
    // 新 new_store 门店 是否与 old_store 门店 是否一样 是则不加载，否则反之
    // 且 new_store 、old_store 长度不能为空，否则没有默认分组情况下，无法加载数据
    if (new_store.sort().toString() == old_store.sort().toString() && (new_store.length && old_store.length)) {
      return true
    } else {
      return false
    }

  },

  // 门店分组-去读本地存储default_ids (此处为核心函数！！！！)
  async getStorageDefaultIds() {
    const _that = this;
    console.log('来了，来了')
    return new Promise(resolve => {
      wx.getStorage({
        key: _that.default_ids,
        success: function (res) {
          // 同步 default_ids  重新覆盖 old_default_ids  避免页面 onLoad onShow 加载两次列表
          resolve(res.data)
        },
      })
    })
    // 一下注释为无用业务（暂时不删除）
    // expired_time 是否已过期
    // const expired_time = await new Promise(resolve => {
    //   wx.getStorage({
    //     key: 'expired_time',
    //     success: function (res) {
    //       resolve(res.data)
    //     },
    //   })
    // })

    // 返回门店分组ids
    // const top_groups = await new Promise(async resolve => {
    //   // expired_time 过期则重新请求智慧管店用户信息 
    //   if (expired_time == 0) {
    //     const { data: { top_groups, top_group_name }} = await getSysUserInfo()

    //     // 覆盖本地门店信息
    //     wx.setStorage({ key: 'default_ids', data: res.data.top_groups || [] })
    //     wx.setStorage({ key: 'group_names', data: res.data.top_group_name })
    //     _that.initExpiredTime()

    //     // 返回默认分组
    //     resolve(top_groups)
    //   } else {
    //     resolve([])
    //   }
    // })


    // 是否有门店分组、有则返回、否则返回本地默认的门店分组
    // if (top_groups.length) {
    //   return Promise.resolve(top_groups)
    // } else {
    //   console.log('来了，来了')
    //   return new Promise(resolve => {
    //     wx.getStorage({
    //       key: _that.default_ids,
    //       success: function (res) {
    //         // 同步 default_ids  重新覆盖 old_default_ids  避免页面 onLoad onShow 加载两次列表
    //         resolve(res.data)
    //       },
    //     })
    //   })
    // }
  },

  /**=========================多选门店 end======================== */
  // 处理分享、公众号跳转参数格式化
  getQueryFormat(path, query) {
    const keyMap = Object.keys(query)
    // 处理参数
    let params = ''
    keyMap.map(key => { params += `${key}=${query[key]}&` })
    // 去除最后一个 '&' 符号
    params = params.substr(0, params.length - 1)
    
    return `/${path}?${params}`
  },
  // 注册账号
  goRegister() {
    // wx.reLaunch({
    //   url: '/pages/register/register',
    // })
    wx.reLaunch({
      url: '/pages/registers/registers',
    })
  },
  // 绑定账号
  goBinding() {
    wx.reLaunch({
      url: '/pages/binding/binding',
    })
  },
  // 去登录
  goLogin() {
    wx.reLaunch({
      url: '/pages/login/login',
    })
  },
})
```

## utils/config.js

```js
import { formatTimeTwo } from './util.js';

// dev 开发环境,prod 生产环境
export const ENV = 'dev';

// api 
// http://mini2.returnsaas.com/
export const BASE_API = ENV === 'dev' ? 'https://testapi.returnsaas.com/' : 'https://api.returnsaas.com/' 

export const WEBVIEW_URL = ENV === 'dev' ? 'https://testwebview.returnsaas.com/#' : 'https://webview.returnsaas.com/#' 

export const BASE_API_EDU = ENV === 'dev' ? 'https://testedu.returnsaas.com/' :'https://api.returnsaas.com:5211/'
// 5G手机号码
export const FIVE_G_PHONE = '075536909705'

// 成功码
export const SUCCESS_CODE = 200;

// 未绑定状态码
export const ERROR_BIND = 1011;

// 页脚标题
export const FOOTER_TITLE = '蕾特恩集团·云智能技术提供服务';

// 版本号
export const APP_VSERSION = 'v2.1.53'

// 腾讯地图
export const TENCENT_MAP_KEY = 'BXRBZ-DIWW3-OIB3A-Y72J3-GFX56-ZFBEU';

// 地图的密钥key(为了适配多个端传的一个密钥简单处理)
export const KEY = "83fefdd56176d64fffc34b3ac18d0d07"

// 绑定手机
export const BIND_PHONE = 1;

// 绑定账号
export const BIND_USER_NUM = 1;

// 上传图片数量
export const IMAGE_NUM = 9;

// oss 静态图片地址
export const OSS_IMG_URL = "https://crmimageoss.returnsaas.com/system";

// oss url图片库路径
export const OSS_URL = "https://crmimageoss.returnsaas.com";

// 分享标题
export const SHARE_TITLE_ONE = ['我已升级蕾特恩智慧门店，就差你了，赶紧来加入！','/pages/login/login', OSS_IMG_URL + '/image/zhuanfa2.png',];


export const fileHost = 'https://crmtest2018.oss-cn-hangzhou.aliyuncs.com/'

// aliyun OSS 图片上传配置
export const OSS_UPLOAD_CONF = {
  ossConfig: {
    uploadImageUrl: fileHost, //默认存在根目录，可根据需求改
    AccessKeySecret: 'mqHXF6WjDg9LUlFSKgO9hsY35Oly2V',
    OSSAccessKeyId: 'LTAIXHtCOKU6aSZR',
    timeout: 87600 //这个是上传文件时Policy的失效时间
  },
  // 上传文件路径
  filePath: {
    server: {
      name: '正式服-服务图库上传',
      path: `userservice/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    testservice: {
      name: '测试服-服务图库上传',
      path: `testuserservice/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    user_career_img: {
      name: '用户个人形象照',
      path: 'user_career_image/'
    },
    // 测试网盘
    test_cloud_storage: {
      name: 'test_cloud_storage_upload',
      path: `test_cloud_storage/`
    },
    // 正式网盘
    cloud_storage: {
      name: 'cloud_storage_upload',
      path: `cloud_storage/`
    },
    test_meeting: {
      name: '测试服-会议图库',
      path: `test_meeting_image/app/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    meeting: {
      name: '正式服-会议图库',
      path: `meeting_image/app/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 测试 顾客回访记录图库
    test_customer_return_visit_image: {
      name: '测试服-顾客回访记录图库',
      path: `customer_return_visit_image/test/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 正式 顾客回访记录图库
    customer_return_visit_image: {
      name: '正式服-顾客回访记录图库',
      path: `customer_return_visit_image/prod/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 正式转诊的支付凭证
    transfer_pay_img: {
      name: '转诊的支付凭证上传',
      path: `clinics_transfer/image/`
    },
    // 测试转诊的支付凭证
    test_transfer_pay_img: {
      name: '转诊的支付凭证上传',
      path: `test_clinics_transfer/image/`
    },
    // 正式门店转店收款码
    store_info_receiving_code: {
      name: '门店收款码',
      path: `store_pay_qrcode/qrcode/`
    },
    // 测试门店转店收款码
    test_store_info_receiving_code: {
      name: '门店收款码',
      path: `store_pay_qrcode/test/`
    },
    // 法大大 企业认证文件
    fadada_company_verify: {
      name: '法大大企业认证文件上传',
      path: `fadada_company_verify/`
    },
    // 测试顾客皮检表
    test_customer_analysis: {
      name: '皮检表',
      path: `test_customer_analysis/`
    },
    // 正式顾客皮检表
    customer_analysis: {
      name: '皮检表',
      path: `customer_skin_analysis/`
    },
    // 预约申诉凭证(正式)
    reserve_appeal: {
      name: '预约申诉',
      path: `reserve_appeal/${new Date().getFullYear()}/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 预约申诉凭证(测试)
    test_reserve_appeal: {
      name: '预约申诉',
      path: `test_reserve_appeal/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 分期资料收集(正式)
    staging_information: {
      name: '分期资料收集',
      path: `staging_information/${new Date().getFullYear()}/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 分期资料收集(测试)
    test_staging_information: {
      name: '分期资料收集',
      path: `test_staging_information/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 抖音团购开通资料收集(正式)
    douyin_group_purchase: {
      name: '抖音团购开通资料收集',
      path: `douyin_group_purchase/${new Date().getFullYear()}/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 抖音团购开通资料收集(测试)
    test_douyin_group_purchase: {
      name: '抖音团购开通资料收集',
      path: `test_douyin_group_purchase/${new Date().getFullYear()}/${formatTimeTwo(new Date().getTime() / 1000, 'M')}/${new Date().getDate()}/`
    },
    // 门店积分商品主图(正式)
    store_point_image: {
      name: '门店积分商品主图',
      path: `store_point_main_image/`
    },
    // 门店积分商品主图(测试)
    test_store_point_image: {
      name: '门店积分商品主图',
      path: `test_store_point_main_image/`
    },
  },
}
// 门店类型
export const RESOURCE_MANAGEMENT = [
  { value: 1, name: '生美' }
]
// 角色分配
export const ROLE_NAME = {
  'promoter': { id: 1, name: '推广员' },
  'reception': { id: 2, name: '前台' },
  'shopowner': { id: 3, name: '店长' },
  'area_manager': { id: 4, name: '区域经理' },
  'network': { id: 5, name: '营销管理' },
  'finance': { id: 6, name: '财务部门' },
  'ceo': { id: 7, name: '集团管理' },
  'entry_clerk': { id: 8, name: '系统维护' },
  'admin': { id: 9, name: '最高管理员' },
  'service': { id: 10, name: '美容师' },
  'sub_manager': { id: 11, name: '子区域经理' },
  'erp_manager': { id: 12, name: '进销存管理员' },
  'ivm_manager': { id: 13, name: '招商管理' }
}

// 优惠券类型
export const COUPON_SYS_TYPE = {
  '0' : '门店优惠券',
  '1' : '会员礼',
  '2' : '新人礼包',
  '3' : '直播券',
  '4': '等级权益券',
  '5': '活动奖品券'
}

// 分享参数类型配置
export const shareConfig = {
  // 邀请蕾特恩家人
  invite: 'invite',
  // 客诉周报
  customer_complaint: 'customer_complaint'
}

// 分享参数类型配置
export const SHARE_TYPE = {
  // 邀请蕾特恩家人
  1: 'invite',
  // 客诉周报
  2: 'customer_complaint',
  // webview 会议邀请嘉宾
  3: 'webview_meeting'
}
```

## utils/requests.js

```js
/**
 * http请求 
 */
import { Message } from './util.js';
import { BASE_API, BASE_API_EDU } from './config.js'
// 去登录
function goLogin() {
  wx.reLaunch({
    url: '/pages/login/login',
  })
}
// 默认参数
const defaultOptions = {
  url: '', 
  header: {},
  config: {
    needToken: true,
    needLoading: true,
    needMsg: true
  }
}
const requests = {
  // 处理传入参数
  getOptions(options){
    if (options){
      // 合并参数
      for (let item in defaultOptions) {
        if (options[item] == undefined) {
          options[item] = defaultOptions[item];
        } else {
          // 处理config 参数 赋值问题
          options.config = Object.assign({}, defaultOptions.config, options.config)
        }

      }
      const defaultHader = {
        'content-type': 'application/json',
        'Authorization': wx.getStorageSync('access_token') || '',
        'version': 'v2'
      }
      // 请求头
      options.header = Object.assign({}, defaultHader, options.header)
      options.config = Object.assign({}, defaultHader.config, options.config)
      return options;
    }
  },
  // GET 请求
  get(options) {
    return this.requestAll(options,'GET');
  },
  // POST 请求
  post(options) {
    return this.requestAll(options, 'POST');
  },
  // 网络请求
  requestAll(options, method){
    return new Promise((resolve, reject) => {
      let option = this.getOptions(options);
      
      if (option.config.needLoading) {
        Message.loading.show('请稍后');
      }
      const edu = 'wechat/media_recommend/case/list'
      wx.request({
        url: `${option.url == edu ? BASE_API_EDU : BASE_API}${option.url}`,
        data: option.data || {},
        header: option.header,
        method: method,
        success: function (res) {
          // 是否存在状态码 code
          if (res.data.hasOwnProperty('code')) {
            res.data.code = Number(res.data.code)
          }
          if (res.data.code == 200) {
            // 如返回新的access_token 则保存
            if (res.header.Authorization) {
              wx.setStorageSync('access_token', res.header.Authorization)
            }
            resolve(res.data);
          } else {
            reject(res.data);
          }
        },
        fail: function (res) {
          console.log('request fail', res)
          reject(res);
        },
        complete: function (res) {
          if (option.config.needLoading) {
            Message.loading.hide();
          }
          if (res.data) {
            // 非 500 404
            if (option.config.needMsg && res.data.hasOwnProperty('code') && res.data.code != 200) {
              Message.warns(res.data.msg)
            }
            // 4020 账号异常 4021 access_token 过期 4022 尚未登录
            if ([4020, 4021, 4022, 402].includes(res.data.code)) {
              Message.warns(res.data.msg)
              // 清除本地所有缓存，重新登录
              wx.clearStorage()
              goLogin()
            }
            // 4017: 账号尚未绑定
            if (res.data.code == 4017) {
              Message.warns(res.data.msg)
              wx.clearStorage()
              goLogin()
            }
            // 1020: 该手机号已经被绑定
            // if (res.data.code == 1020) {
            //   Message.warns(res.data.msg)
            //   wx.clearStorage()
            //   goLogin()
            // }
          } else {
            // 500
            if (res.statusCode == 500) {
              Message.warns('500 Internal Server Error')
            }
            // 请求超时
            if (res.errMsg.indexOf('timeout') >= 0) {
              Message.warns('请求超时，请稍后重试')
            }
          }
        },
      })
    })
  }
}

module.exports = {
  requests,
}
```

































