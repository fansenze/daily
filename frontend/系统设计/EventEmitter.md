## EventEmitter

这两天正好重新整理了小程序端的架构 (因为要新起一个大项目...简直了), 所以就顺手写了这个模块  
No BB, 就直接上代码

### 基础类

```javascript
// event/event_emitter.js

// 伪造一个私有变量, 只能通过类上挂载的方法调用, 无法实际访问到
const events = new Map()

class EventEmitter {
  constructor (props = {}) {
    this.isDev = !!props.isDev
  }

  once (eventName, callback) {
    if (typeof callback === 'function') {
      events.set(
        eventName,
        [callback, this.off.bind(this, eventName)]
      )
      this.log('info', eventName, `Add an event listener that only emits once`)
    } else {
      this.log('warn', eventName, `callback must be function`)
    }
  }

  on (eventName, callback) {
    if (typeof callback === 'function') {
      events.set(eventName, callback)
      this.log('info', eventName, `Add an event listener`)
    } else {
      this.log('warn', eventName, `callback must be function`)
    }
  }

  off (eventName) {
    const isSuccess = events.delete(eventName)
    isSuccess
      ? this.log('info', eventName, `Remove the event listener`)
      : this.log('warn', eventName, `Event listener is not found`)
    return isSuccess
  }

  emit (eventName, ...args) {
    const callback = events.get(eventName)
    if (!callback) {
      return this.log('warn', eventName, `Event listener is not found`)
    }
    typeof callback === 'function'
      ? callback(...args) // eslint-disable-line
      : callback.forEach(e => e(...args))
  }

  log (type, eventName, msg) {
    this.isDev && console[type](`[Event Emitter]\n  - Event Name: "${eventName}"\n  - Message: ${msg}`)
  }
}

export default EventEmitter
```

### 类的实例化

```javascript
// event/export.js

// 模块引用暴露
import EventEmitter from './event_emitter.js'

export default new EventEmitter({
  isDev: true
})
```

### Usage

```javascript
// event/usage.js

import $EventEmitter from './export.js'

const event1 = Symbol('1')
const event2 = 'event2'

$EventEmitter.on(event1, console.log)
$EventEmitter.emit(event1, 'hello world') // "hello world"
$EventEmitter.emit(event1, 'hello man')   // "hello man"
$EventEmitter.off(event1)                 // true

$EventEmitter.once(event1, console.log)
$EventEmitter.emit(event1, 'hello world') // "hello world"
$EventEmitter.emit(event1, 'hello world') // [ warning ]
```
