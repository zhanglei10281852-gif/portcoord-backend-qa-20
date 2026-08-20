# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

逾期泊位窗口生成了升级记录，但窗口自身的升级级别和责任方没有同步。请修复升级状态的一致性。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/portcoord-backend-qa-20
- 仓库地址：https://github.com/zhanglei10281852-gif/portcoord-backend-qa-20.git
- parent SHA：f17128526cf5596d0566109632d6b414d1221cda

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/portcoord-backend-qa-20.git bug-repro
cd bug-repro
git checkout --detach f17128526cf5596d0566109632d6b414d1221cda
go test ./internal/berthing -run "^TestBerthing_EscalateOverdueDeadlineExceeded$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/berthing -run "^TestBerthing_EscalateOverdueDeadlineExceeded$" -count=1
--- FAIL: TestBerthing_EscalateOverdueDeadlineExceeded (0.01s)
    berthing_test.go:174: expected 1 escalation, got 0
FAIL
FAIL	portcoord/internal/berthing	0.015s
FAIL

```

stderr：

```text
warning: internal/berthing/berthing_test.go has type 100755, expected 100644
warning: internal/berthing/berthing_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/berthing -run "^TestBerthing_EscalateOverdueDeadlineExceeded$" -count=1
--- FAIL: TestBerthing_EscalateOverdueDeadlineExceeded (0.30s)
    berthing_test.go:174: expected 1 escalation, got 0
FAIL
FAIL	portcoord/internal/berthing	0.540s
FAIL

```

stderr：

```text
warning: internal/berthing/berthing_test.go has type 100755, expected 100644
warning: internal/berthing/berthing_test.go has type 100755, expected 100644

```

## 通过条件

在题面触发条件下，公开行为必须恢复且原始异常不再出现；定向命令 go test ./internal/berthing -run ^TestBerthing_EscalateOverdueDeadlineExceeded$ -count=1、相关包测试、全量测试、race、vet 和 build 必须通过；不得删除或跳过测试，也不得绕过目标逻辑。
