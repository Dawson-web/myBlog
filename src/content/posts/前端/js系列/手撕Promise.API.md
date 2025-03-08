---
title: 手撕Promise.API
tags: [Js系列]
categories: Js系列
published: 2024-08-25 21:00:00
draft: false
image: /前端/js系列/手撕Promise.API/cover.webp

---
---


## 手撕Promise.all

```js
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(88);
  }, 100);
});
const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(99);
  }, 100);
});

const myPromiseAll = (array) => {
  let results = [];
  let count = 0;
  return new Promise((resolve, reject) => {
    for (let index in array) {
      if (array[index] instanceof Promise) {
        array[index].then(
          (res) => {
            results[index] = res;
            count++;
            if (count === array.length) {
              resolve(results);
            }
          },
          (err) => {
            reject(err);
          }
        );
      } else {
        results[index] = array[index];
        count++;
        if (count === array.length) {
          resolve(results);
        }
      }
    }
  });
};

let arr = [promise1, promise2, 3, 4];

myPromiseAll(arr)
  .then((res) => {
    console.log(res, "res");
  })
  .catch((err) => {
    console.log(err, "err");
  });


```

## 手撕Promise.race

```js

const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(88);
  }, 1000);
});
const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject("error");
  }, 500);
});

const myPromiseRace = (array) => {
  let results = "";
  return new Promise((resolve, reject) => {
    for (let index in array) {
      if (array[index] instanceof Promise) {
        array[index].then(
          array[index].then(
            (res) => {
              results = res;
              resolve(results);
            },
            (err) => {
              reject(err);
            }
          )
        );
      } else {
        results = array[index];
        resolve(results);
      }
    }
  });
};

let arr = [promise1, promise2];

myPromiseRace(arr)
  .then((res) => {
    console.log(res, "res");
  })
  .catch((err) => {
    console.log(err, "err");
  });


let arr1 = [promise1, promise2, 3, 4];

myPromiseRace(arr1)
  .then((res) => {
    console.log(res, "res");
  })
  .catch((err) => {
    console.log(err, "err");
  });



```

## 手撕Promise.race
```js
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject("error");
  }, 500);
});

const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(88);
  }, 1000);
});

const myPromiseAny = (array) => {
  let results = "";
  return new Promise((resolve, reject) => {
    for (let index in array) {
      if (array[index] instanceof Promise) {
        array[index].then(
          array[index].then(
            (res) => {
              results = res;
              resolve(results);
            },
            (err) => {
              console.log("跳过错误");
            }
          )
        );
      } else {
        results = array[index];
        resolve(results);
      }
    }
  });
};

let arr = [promise1, promise2];

myPromiseAny(arr)
  .then((res) => {
    console.log(res, "res");
  })
  .catch((err) => {
    console.log(err, "err");
  });


```

## 手撕Promise.allSettled

```js
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(88);
  }, 100);
});
const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject("error 99");
  }, 100);
});

const myPromiseAllSettled = (array) => {
  let results = [];
  let count = 0;
  return new Promise((resolve, reject) => {
    for (let index in array) {
      if (array[index] instanceof Promise) {
        array[index].then(
          (res) => {
            results[index] = { result: res, status: "fulfilled" };
            count++;
            if (count === array.length) {
              resolve(results);
            }
          },
          (err) => {
            results[index] = { error: err, status: "rejected" };
            count++;
            if (count === array.length) {
              resolve(results);
            }
          }
        );
      } else {
        results[index] = { result: array[index], status: "fulfilled" };
        count++;
        if (count === array.length) {
          resolve(results);
        }
      }
    }
  });
};

let arr = [promise1, promise2, 3, 4];

myPromiseAllSettled(arr)
  .then((res) => {
    console.log(res, "res");
  })
  .catch((err) => {
    console.log(err, "err");
  });



```


---