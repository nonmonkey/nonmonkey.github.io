**深度优先遍历(DFS)**

> 自上而下遍历，不需要记住所有的节点, 所以占用空间小, 深度优先有回溯的操作(没有路走了需要回头)所以相对而言时间会长一点。

**广度优先遍历(BFS)**

> 逐层遍历，需要先记录所有的节点，占用空间大，采用的是队列的形式, 即先进先出。


### 1.深克隆
```javascript
function _isObject(val) {
  return Object.prototype.toString.call(val) === '[object Object]';
}
function _isArray(val) {
  return Array.isArray ? Array.isArray(val) : Object.prototype.toString.call(val) === '[object Array]';
}

// DFS
function DFSDeepClone(sourceObj) {
  // visited和myVisited用于避免重复遍历
  const visited = []; // 存放sourceObj中已经访问过的引用值(数据，对象)
  const myVisited = []; // 存放新创建出来的引用值(数据，对象)

  function _deepClone(source, visited = []) {
    let clone = null;
    if (_isObject(source) || _isArray(source)) {
      const index = visited.indexOf(source); // 判断该对象是否已经访问过
      if (index > -1) {
        clone = myVisited[index]; // 已访问过的在myVisited中直接取值
      } else {
        visited.push(source);
  
        clone = _isObject(source) ? {} : [];
        myVisited.push(clone);
        for (let [key, val] of Object.entries(source)) {
          clone[key] = _deepClone(val, visited);
        }
      }
    } else {
      clone = source;
    }
    return clone;
  }

  return _deepClone(sourceObj, visited);
}

// BFS
function BFSDeepClone(sourceObj) {
  let target = null;// 最终返回值
  if (_isObject(sourceObj) || _isArray(sourceObj)) {
    // visited和myVisited用于避免重复遍历
    const visited = []; // 存放sourceObj中已经访问过的引用值(数据，对象)
    const myVisited = []; // 存放新创建出来的引用值(数据，对象)
    const queue = []; // 队列
    target = _isObject(sourceObj) ? {} : [];

    visited.push(sourceObj);
    myVisited.push(target);
    queue.push({
      source: sourceObj,
      clone: target,
    });

    while (queue.length !== 0) {
      const first = queue.shift();
      const clone = first.clone;

      for (const [key, val] of Object.entries(first.source)) {
        if (_isObject(val) || _isArray(val)) {
          let childClone = null;
          const index = visited.indexOf(val); // 判断该对象是否已经访问过
          if (index > -1) {
            childClone = myVisited[index]; // 已访问过的在myVisited中直接取值
          } else {
            visited.push(val);
            childClone = _isObject(val) ? {} : [];
            myVisited.push(childClone);

            queue.push({
              source: val,
              clone: childClone,
            });
          }
          clone[key] = childClone;
        } else {
          clone[key] = val;
        }
      }
    }
  } else {
    target = sourceObj;
  }

  return target;
}
```

**测试**
```javascript
const obj = { a:111111, b: 2222222 };
const source = [ 1, { obj: obj, s:11111 }, 6, [ 0, { obj: obj, f:11111111 } ] ];
function test(cloneFunc, source) {
  const clone = cloneFunc(source);
  console.log(
    clone[1].obj === clone[3][1].obj,
    clone[1].obj === source[1].obj,
    clone[3][1].obj === source[3][1].obj,
    JSON.stringify(clone) === JSON.stringify(source),
  );
  return clone;
}

const DFSClone = test(DFSDeepClone, source); // true false false true
const BFSClone = test(BFSDeepClone, source); // true false false true

// 修改某值，clone出的值和source值不同，说明克隆值改变未对source产生影响
DFSClone[1].s = 3;
console.log('s:', DFSClone[1].s, source[1].s); // 3 11111
BFSClone[1].s = 0;
console.log('s:', BFSClone[1].s, source[1].s); // 0 11111
```
