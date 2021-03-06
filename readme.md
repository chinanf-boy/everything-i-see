# 目录

现如今的知识，或者编写技巧，更多来自，知与不知。并不是说问题不难，而是会有积极的，友好的人们，对其简化。将它变成一个库，一个工具，一个软件，更有可能是一段代码，一串命令，一点思维。
而这个存档，是放我之后看到的，在源码/库层面的技巧与捷径。如果你问以前的，`(*^_^*)` 我已经忘了。所以，这个存档出现了。

如何存，我会从，

1. （what）这个工具/技巧是什么，(留下收录时间)，
2. （where） 它被我在哪个项目（指向有名的，且有长期维护的项目）看到，
3. （how） 以及，使用方法。（当然，是不详尽的，仅给出片段解释与关键代码。

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [目录](#目录)
  - [NPM package](#npm-package)
    - [comlink （2022-4-1）](#comlink-2022-4-1)
      - [diff.ts](#diffts)
      - [diff.worker.ts](#diffworkerts)
  - [一串命令](#一串命令)
    - [playwright（2022-4-1）](#playwright2022-4-1)
  - [一点思维](#一点思维)
    - [Vue](#vue)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## NPM package

### [comlink](https://github.com/GoogleChromeLabs/comlink) （2022-4-1）

> 工作线程的简化工具

示例项目：[antfu/vite-plugin-inspect](https://github.com/antfu/vite-plugin-inspect/tree/bc525c40ab700ff05185048c687430608c63759c)

<details>

- [DiffEditor.vue](https://github.com/antfu/vite-plugin-inspect/blob/bc525c40ab700ff05185048c687430608c63759c/src/client/components/DiffEditor.vue#L66)

```ts
// src/client/components/DiffEditor.vue
import { calucateDiffWithWorker } from "../worker/diff";

/* 这个工作函数的用处是，对比两个 文件，经过 vite 插件转换的之后对比，（也就类比，两个文件的不同比较 ）*/
const changes = await calucateDiffWithWorker(l, r);
```

#### diff.ts

```ts
import type { Remote } from "comlink";
import { wrap } from "comlink";
import type { Exports } from "./diff.worker";

let diffWorker: Remote<Exports> | undefined;

export const calucateDiffWithWorker = async (left: string, right: string) => {
  if (!diffWorker) {
    diffWorker = wrap(
      new Worker(new URL("./diff.worker.ts", import.meta.url), {
        type: "module",
      })
    );
  }

  const result = await diffWorker.calucateDiff(left, right);
  return result;
};
```

#### diff.worker.ts

```ts
import { expose } from "comlink";
import { diff_match_patch as Diff } from "diff-match-patch";

const calucateDiff = (left: string, right: string) => {
  const diff = new Diff();
  const changes = diff.diff_main(left, right);
  diff.diff_cleanupSemantic(changes);
  return changes;
};

const exports = {
  calucateDiff,
};

export type Exports = typeof exports;

expose(exports);
```

</details>

## 一串命令

### playwright（2022-4-1）

如果这个自动化框架，存在于项目开发依赖中。
因其使用 electron 作为其依赖，自然它需要下载资源，而这个资源在国内网络下载失败的可能相当大，而这里给出一串命令，替换底层的下载代码的环境变量。

先

```bash
# Linux
export ELECTRON_MIRROR="https://npm.taobao.org/mirrors/electron/"
# Windows
set ELECTRON_MIRROR="https://npm.taobao.org/mirrors/electron/"
# 在进行
yarn
```

## 一点思维

### Vue

- [Vue 项目工程 -- 战斗思维与技巧](vue-project-thought.md)