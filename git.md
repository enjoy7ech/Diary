## git输出增量包

获取两次提交之间不同的代码时，添加过滤条件，不取删除的文件即可，添加–diff-filter=ACMR
git diff --diff-filter 可以过滤几种不同的变化：

--diff-filter=[(A|C|D|M|R|T|U|X|B)…[*]]

Added (A), Copied ( C), Deleted (D), Modified (M), Renamed ®, have their type (i.e. regular file, symlink, submodule, …) changed (T), are Unmerged (U), are Unknown (X), or have had their pairing Broken (B).

```
git archive -o ../update.zip NEW_COMMIT_ID $(git diff --diff-filter=ACMR --name-only OLD_COMMIT_ID)
```

