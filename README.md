# Cloudflare Worker-网站监控

## 前言
监视您的网站，展示状态（包括每日历史记录），并在您的网站状态发生变化时获得Slack/Telegram/Discord通知。使用Cloudflare Workers，CRON触发器和KV存储。

## 在线体验
[网站监控网址：](https://cf-workers-status-page-production.qikaile.workers.dev/)
![](https://cdn.jsdelivr.net/gh/Qikaile/cdn/img/status_page.png)

## 准备
你需要一个cloudflare账号和GitHub 账号

## 部署
您可以使用GitHub Actions使用 **Cloudflare Deploy Button** 进行部署，也可以自行部署。

### 使用Cloudflare部署按钮进行部署
[![Deploy to Cloudflare Workers](https://camo.githubusercontent.com/1f3d0b4d44a2c3f12c78bd02bae907169430e04d728006db9f97a4befa64c886/68747470733a2f2f6465706c6f792e776f726b6572732e636c6f7564666c6172652e636f6d2f627574746f6e3f706169643d74727565)](https://deploy.workers.cloudflare.com/?url=https://github.com/eidam/cf-workers-status-page)

1、单击按钮并按照说明进行操作，最后应得到此存储库的副本

2、导航到新的 **GitHub repository &gt; Settings &gt; Secrets** ，然后添加以下secrets
```yaml
- Name: CF_API_TOKEN (应该自动添加)

- Name: CF_ACCOUNT_ID (应该自动添加)

- Name: SECRET_SLACK_WEBHOOK_URL (可选的)
- Value: your-slack-webhook-url

- Name: SECRET_DISCORD_WEBHOOK_URL (可选的)
- Value: your-discord-webhook-url

```
CF_API_TOKEN的获取：Token 选<font color=red>编辑 Cloudflare Workers</font>，区域资源包括所有区域，账号资源包括你自己账号。
![](https://cdn.jsdelivr.net/gh/Qikaile/cdn/img/2021-02-25-01.png)
![](https://cdn.jsdelivr.net/gh/Qikaile/cdn/img/2021-02-25-02.png)
![](https://cdn.jsdelivr.net/gh/Qikaile/cdn/img/2021-02-25-03.png)

3、导航到存储库中的 **Actions** 设置并启用它们

4、编辑config.yaml以调整配置并列出要监视的所有网站/API

 ```yaml
   settings:
     title: 'Status Page'
     url: 'https://status-page.eidam.dev' # used for Slack & Discord messages
     logo: logo-192x192.png # image in ./public/ folder
     daysInHistogram: 90 # number of days you want to display in histogram
     collectResponseTimes: false # experimental feature, enable only for <5 monitors or on paid plans

     # configurable texts across the status page
     allmonitorsOperational: 'All Systems Operational'
     notAllmonitorsOperational: 'Not All Systems Operational'
     monitorLabelOperational: 'Operational'
     monitorLabelNotOperational: 'Not Operational'
     monitorLabelNoData: 'No data'
     dayInHistogramNoData: 'No data'
     dayInHistogramOperational: 'All good'
     dayInHistogramNotOperational: 'Some checks failed'

   # list of monitors
   monitors:
     - id: workers-cloudflare-com # unique identifier
       name: workers.cloudflare.com
       description: 'You write code. They handle the rest.' # default=empty
       url: 'https://workers.cloudflare.com/' # URL to fetch
       method: GET # default=GET
       expectStatus: 200 # operational status, default=200
       followRedirect: false # should fetch follow redirects, default=false
   ```

5、推送至main分支以触发部署

6、🎉

7、（可选的）转到Cloudflare Workers设置并分配自定义域/路由

例如status-page.eidam.dev/* （确保包括在内，/*因为Worker还提供静态文件）

8、（可选的）编辑[wrangler.toml](./wrangler.toml)以调整Worker设置或CRON触发器计划，特别是如果您处于[Workers Free plan](#workers-kv-free-tier)中

## 说明
本项目是来自：https://github.com/eidam/cf-workers-status-page
