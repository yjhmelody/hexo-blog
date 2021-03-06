---
title: 《Web API的设计与开发》读书笔记
date: 2017-08-08 12:11:10
tags: [Web, API, Book]
category: [CS]
---
# 第1章 什么是 Web API

## Web API 的重要性

一些在线服务能够对那些公开的API进行会话控制、访问控制、服务分析，提供面向用户的仪表盘，以及发布文档等，承接了各种各样的工作。

通过对外公开 Web API，同外部其他服务的集成变得更加便捷，并从中衍生出了新的价值，使在线服务以及业务不断发展，逐步形成了“API经济学”的景象，并在这几年受到相当大的关注。

## 各种各样的 API 模式

* 将已发布的 Web 在线服务的数据或功能通过 API 公开
* 将附加在其他网页上的微件公开
* 构建现代 Web 应用
* 开发智能手机应用
* 公司内部多个系统的集成

## 应该通过 API 公开什么

* 最简洁的答案是将你的在线服务所能做的事情全部通过API公开
* 不存在彻底屏蔽搜集信息的行为，所以无需担心盗用，公开 API并不意味着毫无限制的访问
* 公开 API 将原来的服务组合成新的应用来为用户提供服务的“间接销售”模式

## 设计优美 API 的重要性

* 易于使用
* 便于更改
* 健壮性好
* 不怕公之于众

## 如何美化 Web API

两个重要原则：

* 设计规范明确的内容必须遵守相关规范
* 没有设计规范的内容必须遵守相关事实标准

## REST 与 Web API

REST 依次一般指下面两种意思：

* 符合 Fielding 的 REST 架构风格的Web服务系统
* 符合 RPC风格的 XML (或JSON) + HTTP 接口的系统(不使用SOAP)

## 小结

* 如果尚未公开 Web AP，则应立即考虑公开
* 设计优美的 Web API
* 不用过分拘泥于 REST 一词

