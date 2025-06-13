# 1.FAQ

{% hint style="success" %}
对应的[官方页面地址](https://github.com/dani-garcia/vaultwarden/wiki/FAQs)
{% endhint %}

## Vaultwarden 是否与 Bitwarden 项目或 Bitwarden, Inc 有关？ <a href="#is-vaultwarden-associated-with-the-bitwarden-project-or-bitwarden" id="is-vaultwarden-associated-with-the-bitwarden-project-or-bitwarden"></a>

简短的回答，**没有**。两个项目的开发人员之间有时会有联系，但没有合作。除此之外，Vaultwarden 项目仅使用 Bitwarden, Inc 提供的 Web Vault 并打了一些[补丁](https://github.com/dani-garcia/bw_web_builds/tree/master/patches)，以使其与我们的实现兼容。

## 我发现了一个 Vaultwarden 公共实例。它与这个项目有关吗？使用它安全吗？  <a href="#ive-found-a-public-vaultwarden-instance.-is-it-associated-with-this-project-is-it-safe-to-use" id="ive-found-a-public-vaultwarden-instance.-is-it-associated-with-this-project-is-it-safe-to-use"></a>

本项目不托管任何公共 Vaultwarden 实例，我们也不提倡使用任何此类网站。参见 [#3233](https://github.com/dani-garcia/vaultwarden/discussions/3233#discussioncomment-4917141) [#4142](https://github.com/dani-garcia/vaultwarden/discussions/4142) [#4367](https://github.com/dani-garcia/vaultwarden/discussions/4367#discussioncomment-8529763)

## Vaultwarden 能连接到 Oracle MySQL V8.x 数据库吗？ <a href="#can-bitwarden_rs-connect-to-an-oracle-mysql-v-8-x-database" id="can-bitwarden_rs-connect-to-an-oracle-mysql-v-8-x-database"></a>

在使用 Oracle MySQL v8.x 时，当您试图启动 Vaultwarden，可能会出现以下警告：

```
[vaultwarden::util][WARN] Can't connect to database, retrying: DieselConError.
[CAUSE] BadConnection(
    "Authentication plugin \'caching_sha2_password\' cannot be loaded: /usr/lib/x86_64-linux-gnu/mariadb18/plugin/caching_sha2_password.so: cannot open shared object file: No such file or directory",
)
```

默认情况下，Oracle MySQL v8.x 使用更安全的密码散列方法，这是好事，但我们的构建目前不支持它。

您需要以一种特定的方法创建 Vaultwarden 用户，以便它能使用旧的原生密码散列：

```sql
-- 在 MySQLv8 安装上使用此命令
CREATE USER 'vaultwarden'@'localhost' IDENTIFIED WITH mysql_native_password BY 'yourpassword';
```

如果您已经创建了用户，并且只想更改散列方法，请使用以下命令：

```sql
-- 将密码类型从 caching_sha2_password 更改为 native
ALTER USER 'vaultwarden'@'localhost' IDENTIFIED WITH mysql_native_password BY 'yourpassword';
```

另外可参阅：[使用 MariaDB - 创建数据库和用户](../configuration/database/using-the-mariadb-mysql-backend.md#create-database-and-user)

## 客户端（桌面端、移动端、网页端）无法正常工作，无法登录或提示证书无效。 <a href="#my-client-desktop-mobile-web-does-not-work-i-can-not-login-or-it-complains-about-invalid-certificate" id="my-client-desktop-mobile-web-does-not-work-i-can-not-login-or-it-complains-about-invalid-certificate"></a>

Bitwarden 客户端需要一个安全的连接，才能正常工作且没有任何问题。虽然某些客户端也可以在没有安全连接的情况下工作，但我们并不推荐这样做。

大多数时候，当人们使用证书时仍然有问题，是因为他们使用的是所谓的自签名证书。虽然这些证书可以提供安全连接，但一些平台不允许或不支持它。

我们建议使用诸如 Let's Encrypt 这样的服务来提供一个有效的、被大多数设备默认接受的证书。请参阅以下页面：

* [启用 HTTPS](../reverse-proxy/https/enabling-https.md)
* [使用 Let's Encrypt 证书运行私有 Vaultwarden 实例](../reverse-proxy/https/running-a-private-vaultwarden-instance-with-lets-encrypt-certs.md)

## 为什么我密码库的所有项目都看不到图标？ <a href="#why-do-i-see-no-icons-for-all-my-vault-items" id="why-do-i-see-no-icons-for-all-my-vault-items"></a>

没有显示图标的原因有很多种。如果只是某几个密码库项目，可能是我们无法提取它。有些网站启用了一些保护措施，导致我们的实施失败。他们中的大多数需要 Javascript 才能工作。

这也可能是 Vaultwarden 服务器无法访问互联网或未解决 DNS 查询所致。您可以检查 `/admin/diagnostics` 页面（参阅[启用管理页面](../configuration/enabling-admin-page.md)），看看您是否能解决 DNS 查询以及是否有连接到互联网。如果都没问题，也有可能是防火墙或外发互联网代理阻止了这些请求。

## Websocket 连接显示错误的 IP 地址。 <a href="#websocket-connections-show-wrong-ip-address" id="websocket-connections-show-wrong-ip-address"></a>

这不是我们可以解决的问题。我们使用的库不支持任何形式的 `X-Forwarded-For` 或 `Forward` 标头。

它会始终显示所使用的反向代理的 IP，除非您在没有任何代理的情况下直接运行 Vaultwarden，或者运行透明代理，这可能会让它显示正确的 IP。这不是一个重要的日志记录部分，并且如果您使用反向代理，您可能还可以在其日志中看到此请求，该请求具有正确的 IP。

## 为什么 Vaultwarden 会提示 `[INFO] No .env file found`。即使我已经提供了一个？

启动时，Vaultwarden 将检查进程的当前工作目录中是否存在名为 `.env` 的文件（如果未通过环境变量 `ENV_FILE` 更改）。如果您没有提供此文件，Vaultwarden 将简单地通知您它没有找到它。此文件与您提供给 docker 的用于在创建容器时加载环境变量的 env 文件无关，也与在 [systemd .service](../alternative-deployments/creating-a-systemd-service.md) 中使用的环境文件无关。

## 可以将 Vaultwarden 作为 Azure WebApp 运行吗？ <a href="#can-i-run-bitwarden_rs-as-an-azure-webapp" id="can-i-run-bitwarden_rs-as-an-azure-webapp"></a>

不幸的是，Azure WebApp 使用 CIFS/Samba 作为卷存储，而 CIFS/Samba 不支持锁定。这导致 Vaultwarden 不能使用 SQLite 数据库文件。

有两种解决方式：

1. 不要使用 SQLite，改为使用 MariaDB/MySQL 或 Posgresql 作为数据库后端。
2. 尝试将 `ENABLE_DB_WAL` 环境变量的值设置为 `false` 以禁用 WAL。这需要在一个新的文件上完成，所以您需要移除之前创建的 `db.sqlite3` 文件，并再次重启 Vaultwarden 应用程序。

## 我在 FAQ 中找不到答案，下一步该怎么做？ <a href="#i-did-not-find-my-answer-here-in-the-faq-what-to-do-next" id="i-did-not-find-my-answer-here-in-the-faq-what-to-do-next"></a>

请尝试在我们精彩的 [Wiki](../) 中搜索和点击。如果这对您没有帮助，请尝试查看 [Github 讨论](https://github.com/dani-garcia/bitwarden_rs/discussions)或 [Vaultwarden 论坛](https://bitwardenrs.discourse.group/)。如果这也没有解决，您可以尝试搜索开放的和已关闭的[话题](https://github.com/dani-garcia/bitwarden_rs/issues)。

如果您仍然没有找到答案，您可以在 [Github 讨论](https://github.com/dani-garcia/bitwarden_rs/discussions)或 [Vaultwarden 论坛](https://bitwardenrs.discourse.group/)上发起一个主题，或者加入我们的[聊天室](https://matrix.to/#/#bitwarden_rs:matrix.org)。
