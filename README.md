# 说明

## Usage

查看 workspaces

```shell
# 查看 Workspace 信息
$ yarn workspaces info
```

执行脚本命令

```shell
# 在某个 workspace 中执行脚本命令
$ yarn workspace <workspace-name> <command>

# 在所有 workspace 中执行脚本命令
$ yarn workspaces run <command>
```

添加依赖

```shell
# 在某个 workspace 中添加依赖
$ yarn workspace <workspace-name> add <package>
```

\*以上为 Yarn 1.x 命令，而 2.x 部分有调整，详看[这里](https://yarnpkg.com/features/workspaces)。

## 软链接查找

假设我们项目的 Workspaces 如下，

```json
{
  "@workspace/project-1": {
    "location": "packages/project-1",
    "workspaceDependencies": ["@workspace/common"],
    "mismatchedWorkspaceDependencies": []
  },
  "@workspace/project-2": {
    "location": "packages/project-2",
    "workspaceDependencies": [],
    "mismatchedWorkspaceDependencies": []
  },
  "@workspace/common": {
    "location": "packages/common",
    "workspaceDependencies": [],
    "mismatchedWorkspaceDependencies": []
  }
}
```

可以看到，其中 `@workspace/project-1` 依赖了 `@workspace/common`，所以 `workspaceDependencies` 不为空。反之，没有引用的话就为空数组。

假设，我们在 `project-1` 中引用了 `common` 中的导出模块，如下：

```js
// packages/common/index.js
export const config = { name: 'Frankie', age: 20 }
```

```js
// packages/project-1/index.js
import { config } from '@workspace/common'
console.log(config)
```

当 `project-1` 中查找 `@workspace/common` 时：

1. 先查找 `project-1` 是否存在对应版本的 `@workspace/common`，若找不到继续往下；
2. 查找 workspace-root 是否存在对应版本的 `@workspace/common`，若找不到继续往下；
3. 查找 NPM 平台是否存在对应版本的 `@workspace/common`，若找到会拉取下来，若找不到继续往下；
4. 抛出错误 Not Found。

## References

- [Yarn Workspace 1.x](https://classic.yarnpkg.com/lang/en/docs/workspaces/)
- [Yarn Workspace 2.x](https://yarnpkg.com/features/workspaces)
- [Monorepo 最佳实践之 Yarn Workspaces](https://juejin.cn/post/7011024137707585544)
