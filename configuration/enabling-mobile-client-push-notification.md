# 7.启用移动客户端推送通知

{% hint style="success" %}
对应的[官方页面地址](https://github.com/dani-garcia/vaultwarden/wiki/Enabling-Mobile-Client-push-notification)
{% endhint %}

从 Vaultwarden 1.29.0 版本开始，您可以激活移动客户端的推送通知，以在移动应用程序、网页扩展程序和网页密码库之间无缝同步您的密码库，而无需手动同步。

### 启用移动客户端推送通知 <a href="#enable-mobile-client-push-notification" id="enable-mobile-client-push-notification"></a>

编辑您的 Vaultwarden docker compose 文件，在环境部分加入以下行：

```
      - PUSH_ENABLED=true
      - PUSH_INSTALLATION_ID=
      - PUSH_INSTALLATION_KEY=
```

要获取 PUSH\_INSTALLATION\_ID 和 PUSH\_INSTALLATION\_KEY，请访问 [https://bitwarden.com/host/](https://bitwarden.com/host/)，输入电子邮件地址，然后您将获得您的 ID 和 KEY。

完成后，重新启动 docker 容器：

```
docker compose up -d vaultwarden
```

{% hint style="danger" %}
刚开始时，同步无法在移动应用程序上正常使用，除非您使用的是最新安装的 Bitwarden 应用程序。您必须重新安装应用程序并重新登录 Vaultwarden 才能使推送通知正常工作。
{% endhint %}