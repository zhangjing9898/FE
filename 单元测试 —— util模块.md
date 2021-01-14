针对helper/util文件，我们编写以下的单测



```
import { isDate, isPlainObject, extend, deepMerge } from '../../src/helpers/util'

// describe方法用来定义一组测试，它可以支持嵌套
// 第一个参数是文件name
describe('helpers:util', () => {
  describe('isXX', () => {
    // test 函数是用来定义单个测试用例，它是测试的最小单元
    test('should validate Date', () => {
      // expect 是断言函数，就是判断代码的实际执行结果与预期结果是否一致，如果不一致就抛出一个错误
      // toBeTruthy() 和 toBeFalsy() 都是自带的方法 判断是否为true or false
      // toBeFalsy 匹配任何 if 语句为假
      // toBeTruthy 匹配任何 if 语句为真
      expect(isDate(new Date())).toBeTruthy()
      expect(isDate(Date.now())).toBeFalsy()
    })

    test('should validate PlainObject', () => {
      expect(isPlainObject({})).toBeTruthy()
      expect(isPlainObject(new Date())).toBeFalsy()
    })
  })

  describe('extend', () => {
    test('should be mutable', () => {
      const a = Object.create(null)
      const b = { foo: 123 }

      extend(a, b)

      expect(a.foo).toBe(123)
    })

    test('should extend properties', function() {
      const a = { foo: 123, bar: 456 }
      const b = { bar: 789 }
      const c = extend(a, b)
      // extend 就是一个继承  只是这时候b的值会覆盖a的值 比如现在 c的bar应该是789 而不是 456
      // toBe 用于检验基本数据类型的值是否相等
      // toBe(123) 为了检测c，foo是不是123
      expect(c.foo).toBe(123)
      expect(c.bar).toBe(789)
    })
  })

  describe('deepMerge', () => {
    test('should be immutable', () => {
      const a = Object.create(null)
      const b: any = { foo: 123 }
      const c: any = { bar: 456 }

      deepMerge(a, b, c)

      expect(typeof a.foo).toBe('undefined')
      expect(typeof a.bar).toBe('undefined')
      expect(typeof b.bar).toBe('undefined')
      expect(typeof c.foo).toBe('undefined')
    })

    test('should deepMerge properties', () => {
      const a = { foo: 123 }
      const b = { bar: 456 }
      const c = { foo: 789 }
      const d = deepMerge(a, b, c)

      expect(d.foo).toBe(789)
      expect(d.bar).toBe(456)
    })

    test('should deepMerge recursively', function() {
      const a = { foo: { bar: 123 } }
      const b = { foo: { baz: 456 }, bar: { qux: 789 } }
      const c = deepMerge(a, b)
      // toEqual 和 toBe 比较类似
      // 用于检验引用数据类型的值，由于js本身object数据类型的本身特性，引用数据类型对比只是指针的对比，但是需要对比对象的每个值
      expect(c).toEqual({
        foo: {
          bar: 123,
          baz: 456
        },
        bar: {
          qux: 789
        }
      })
    })

    test('should remove all references from nested objects', () => {
      const a = { foo: { bar: 123 } }
      const b = {}
      const c = deepMerge(a, b)

      expect(c).toEqual({
        foo: {
          bar: 123
        }
      })

      expect(c.foo).not.toBe(a.foo)
    })

    test('should handle null and undefined arguments', () => {
      expect(deepMerge(undefined, undefined)).toEqual({})
      expect(deepMerge(undefined, { foo: 123 })).toEqual({ foo: 123 })
      expect(deepMerge({ foo: 123 }, undefined)).toEqual({ foo: 123 })

      expect(deepMerge(null, null)).toEqual({})
      expect(deepMerge(null, { foo: 123 })).toEqual({ foo: 123 })
      expect(deepMerge({ foo: 123 }, null)).toEqual({ foo: 123 })
    })
  })
})
```



编写完之后的，执行



```
tnpm run test
```



效果图：



可以看出util文件基本都是ok的 

![image.png](https://i.loli.net/2021/01/14/f75WDSHXhQF2g3L.png)
