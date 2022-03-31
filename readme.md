# 目录

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [目录](#目录)
  - [NPM package](#npm-package)
    - [comlink](#comlink)
      - [diff.ts](#diffts)
      - [diff.worker.ts](#diffworkerts)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## NPM package

### comlink

> 工作线程的简化工具

示例项目：[antfu/vite-plugin-inspect](https://github.com/antfu/vite-plugin-inspect/tree/bc525c40ab700ff05185048c687430608c63759c)

- [DiffEditor.vue]https://github.com/antfu/vite-plugin-inspect/blob/bc525c40ab700ff05185048c687430608c63759c/src/client/components/DiffEditor.vue#L66

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
