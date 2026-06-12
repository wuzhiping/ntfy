# Access control

1. docker compose 启动设置env与volumes，自动创建数据库
```markdown
environment:
  NTFY_AUTH_FILE: /var/lib/ntfy/auth.db
  NTFY_AUTH_DEFAULT_ACCESS: deny-all
volumes:
  - ./db:/var/lib/ntfy/
```
2. ntfy user 命令允许您在 ntfy 用户数据库中添加/删除/更改用户，以及更改密码或角色（ user 或 admin ）。
实际上，您通常只需使用 `ntfy user add --role=admin ... ` 创建一个管理员用户即可完成所有这些操作

```markdown
ntfy user list                     # Shows list of users (alias: 'ntfy access')
ntfy user add phil                 # Add regular user phil  
ntfy user add --role=admin phil    # Add admin user phil
ntfy user del phil                 # Delete user phil
ntfy user change-pass phil         # Change password for user phil
ntfy user change-role phil admin   # Make user phil an admin
ntfy user change-tier phil pro     # Change phil's tier to "pro"
ntfy user hash                     # Generate password hash, use with auth-users config option

# 给 abc topic 授予读写权限
ntfy access phil abc rw
ntfy token add phil
```
