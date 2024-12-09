---
title: My Second Post!
date: "2015-05-06T23:46:37.121Z"
---

# GASDocumentation

我对虚幻引擎5的GameplayAbilitySystem插件（GAS）的理解，结合一个简单的多人示例项目。这不是官方文档，此项目和我本人与Epic Games没有任何关联。我对信息的准确性不做任何保证。

此文档的目的是解释GAS中的主要概念和类，并根据我自己的经验提供一些额外的评论。在社区中，用户对GAS有很多“部落知识”，我希望在这里分享我的所有知识。

示例项目和文档与**虚幻引擎5.3**（UE5）保持一致。该文档有旧版本虚幻引擎的分支，但不再支持，并可能存在错误或过时的信息。请使用与您引擎版本匹配的分支。

[GASShooter](https://github.com/tranek/GASShooter)是一个姐妹示例项目，演示了GAS在多人FPS/TPS中的高级技术。
最好的文档永远是插件源代码。

<a name="table-of-contents"></a>
## 目录
> 1. [游戏能力系统插件简介](#intro)
> 1. [示例项目](#sp)
> 1. [使用GAS设置项目](#setup)
> 1. [概念](#concepts)  
>    4.1 [能力系统组件](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [复制模式](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [设置和初始化](#concepts-asc-setup)  
>    4.2 [游戏标签](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [响应游戏标签变化](#concepts-gt-change)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [从插件 .ini 文件加载游戏标签](#concepts-gt-loadfromplugin)  
>    4.3 [属性](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [属性定义](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [基础值 vs 当前值](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [元属性](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [响应属性变化](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [衍生属性](#concepts-a-derived)  
>    4.4 [属性集](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [属性集定义](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [属性集设计](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [具有单独属性的子组件](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [在运行时添加和移除属性集](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [物品属性（武器弹药）](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [物品上的普通浮动值](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [物品上的 `AttributeSet`](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [物品上的 `ASC`](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [定义属性](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [初始化属性](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [游戏效果](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [游戏效果定义](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [应用游戏效果](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [移除游戏效果](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [游戏效果修饰符](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [乘法和除法修饰符](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [修饰符上的游戏标签](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [游戏效果堆叠](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [授予能力](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [游戏效果标签](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [免疫](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [游戏效果规范](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [游戏效果上下文](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [修饰符大小计算](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [游戏效果执行计算](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [向执行计算发送数据](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [支持数据属性计算修饰符](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [支持数据临时变量计算修饰符](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [游戏效果上下文](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [自定义应用要求](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [消耗游戏效果](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [冷却时间游戏效果](#concepts-ge-cooldown)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [回溯数据临时变量计算修饰符](#concepts-ge-ec-senddata-backingdatatempvariable)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [游戏效果上下文](#concepts-ge-ec-senddata-effectcontext)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [自定义应用需求](#concepts-ge-car)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [成本游戏效果](#concepts-ge-cost)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [冷却游戏效果](#concepts-ge-cooldown)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [获取冷却游戏效果的剩余时间](#concepts-ge-cooldown-tr)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [监听冷却开始和结束](#concepts-ge-cooldown-listen)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [预测冷却时间](#concepts-ge-cooldown-prediction)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [更改活动游戏效果的持续时间](#concepts-ge-duration)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [在运行时创建动态游戏效果](#concepts-ge-dynamic)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [游戏效果容器](#concepts-ge-containers)  
4.6 [游戏能力](#concepts-ga)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [游戏能力定义](#concepts-ga-definition)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [复制策略](#concepts-ga-definition-reppolicy)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [服务器尊重远程能力取消](#concepts-ga-definition-remotecancel)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [直接复制输入](#concepts-ga-definition-repinputdirectly)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [绑定输入到ASC](#concepts-ga-input)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [绑定输入而不激活能力](#concepts-ga-input-noactivate)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [授予能力](#concepts-ga-granting)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [激活能力](#concepts-ga-activating)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [被动能力](#concepts-ga-activating-passive)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [激活失败标签](#concepts-ga-activating-failedtags)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [取消能力](#concepts-ga-cancelabilities)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [获取活动能力](#concepts-ga-definition-activeability)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [实例化策略](#concepts-ga-instancing)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [网络执行策略](#concepts-ga-net)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [能力标签](#concepts-ga-tags)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [游戏能力规范](#concepts-ga-spec)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [将数据传递给能力](#concepts-ga-data)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [能力的成本和冷却时间](#concepts-ga-commit)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [提升能力等级](#concepts-ga-leveling)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [能力集](#concepts-ga-sets)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [能力批处理](#concepts-ga-batching)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [网络安全策略](#concepts-ga-netsecuritypolicy)  
4.7 [能力任务](#concepts-at)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [能力任务定义](#concepts-at-definition)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [自定义能力任务](#concepts-at-definition)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [使用能力任务](#concepts-at-using)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [根运动源能力任务](#concepts-at-rms)  
4.8 [游戏提示](#concepts-gc)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [游戏提示定义](#concepts-gc-definition)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [触发游戏提示](#concepts-gc-trigger)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [本地游戏提示](#concepts-gc-local)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [游戏提示参数](#concepts-gc-parameters)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [游戏提示管理器](#concepts-gc-manager)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [防止游戏提示触发](#concepts-gc-prevention)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [游戏提示批处理](#concepts-gc-batching)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [手动RPC](#concepts-gc-batching-manualrpc)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [一个GE上的多个GC](#concepts-gc-batching-gcsonge)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [游戏提示事件](#concepts-gc-events)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [游戏提示可靠性](#concepts-gc-reliability)  
4.9 [能力系统全局](#concepts-asg)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
4.10 [预测](#concepts-p)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [预测密钥](#concepts-p-key)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [在能力中创建新的预测窗口](#concepts-p-windows)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [预测性生成演员](#concepts-p-spawn)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [GAS中预测的未来](#concepts-p-future)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [网络预测插件](#concepts-p-npp)  
4.11 [目标定位](#concepts-targeting)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [目标数据](#concepts-targeting-data)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [目标演员](#concepts-targeting-actors)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [目标数据过滤器](#concepts-target-data-filters)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [游戏能力世界准星](#concepts-targeting-reticles)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [游戏效果容器目标定位](#concepts-targeting-containers)  
> 1. [常用功能和效果](#cae)    
>    5.1 [眩晕](#cae-stun)    
>    5.2 [冲刺](#cae-sprint)    
>    5.3 [瞄准镜视角（Aim Down Sights）](#cae-ads)    
>    5.4 [吸血](#cae-ls)    
>    5.5 [在客户端和服务器上生成随机数](#cae-random)    
>    5.6 [暴击](#cae-crit)    
>    5.7 [非叠加游戏效果，但只有最大量级的效果实际影响目标](#cae-nonstackingge)    
>    5.8 [在游戏暂停时生成目标数据](#cae-paused)    
>    5.9 [一键交互系统](#cae-onebuttoninteractionsystem)    
> 1. [调试GAS](#debugging)    
>    6.1 [showdebug abilitysystem](#debugging-sd)    
>    6.2 [游戏调试器（Gameplay Debugger）](#debugging-gd)    
>    6.3 [GAS日志记录](#debugging-log)    
> 1. [优化](#optimizations)    
>    7.1 [能力批处理](#optimizations-abilitybatching)    
>    7.2 [游戏提示批处理](#optimizations-gameplaycuebatching)    
>    7.3 [AbilitySystemComponent复制模式](#optimizations-ascreplicationmode)    
>    7.4 [属性代理复制](#optimizations-attributeproxyreplication)    
>    7.5 [ASC延迟加载](#optimizations-asclazyloading)    
> 1. [生活质量建议](#qol)    
>    8.1 [游戏效果容器](#qol-gameplayeffectcontainers)    
>    8.2 [绑定到ASC委托的蓝图异步任务](#qol-asynctasksascdelegates)    
> 1. [故障排除](#troubleshooting)    
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)    
>    9.2 [`ScriptStructCache` 错误](#troubleshooting-scriptstructcache)    
>    9.3 [动画蒙太奇没有复制到客户端](#troubleshooting-replicatinganimmontages)    
>    9.4 [复制蓝图演员时将AttributeSets设置为nullptr](#troubleshooting-duplicatingblueprintactors)    
>    9.5 [未解析的外部符号UEPushModelPrivate::MarkPropertyDirty(int,int)](#troubleshooting-unresolvedexternalsymbolmarkpropertydirty)    
>    9.6 [枚举名称现在用路径名称表示](#troubleshooting-enumnamesarenowpathnames)    
> 1. [常见的GAS缩写](#acronyms)    
> 1. [其他资源](#resources)    
>    11.1 [与Epic Game的Dave Ratti的问答](#resources-daveratti)    
>          11.1.1 [社区问题1](#resources-daveratti-community1)    
>          11.1.2 [社区问题2](#resources-daveratti-community2)    
> 1. [GAS变更日志](#changelog)    
>    * [5.3](#changelog-5.3)    
>    * [5.2](#changelog-5.2)    
>    * [5.1](#changelog-5.1)    
>    * [5.0](#changelog-5.0)    
>    * [4.27](#changelog-4.27)    
>    * [4.26](#changelog-4.26)    
>    * [4.25.1](#changelog-4.25.1)    
>    * [4.25](#changelog-4.25)    
>    * [4.24](#changelog-4.24)  



<a name="intro"></a>
## 1. GameplayAbilitySystem插件简介

根据[官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)的说明：

> Gameplay Ability System是一个高度灵活的框架，旨在构建角色在RPG或MOBA游戏中可能使用的能力和属性。您可以为游戏中的角色构建动作或被动能力，创建状态效果以影响各种属性，实现"冷却"计时器或资源消耗以调节这些动作的使用，改变能力的等级及其效果，激活粒子或声音效果等。简单来说，该系统可以帮助您设计、实现并高效网络化游戏内能力，无论是简单的跳跃，还是您最喜欢的角色在现代RPG或MOBA游戏中的复杂能力集。

该插件由Epic Games开发，并与虚幻引擎一起发布。它在像Paragon和Fortnite等AAA商业游戏中经过了严格测试。

该插件为单人和多人游戏提供现成的解决方案，支持：
* 实现基于等级的角色能力或技能，具有可选的消耗和冷却时间（[GameplayAbilities](#concepts-ga)）
* 操作属于演员的数值`Attributes`（[Attributes](#concepts-a)）
* 对演员应用状态效果（[GameplayEffects](#concepts-ge)）
* 对演员应用`GameplayTags`（[GameplayTags](#concepts-gt)）
* 生成视觉或声音效果（[GameplayCues](#concepts-gc)）
* 复制上述所有内容

在多人游戏中，GAS支持[客户端预测](#concepts-p)：
* 能力激活
* 播放动画蒙太奇
* 属性变化
* 应用`GameplayTags`
* 生成`GameplayCues`
* 通过连接到`CharacterMovementComponent`的`RootMotionSource`功能进行移动

**GAS必须在C++中设置**，但设计师可以在Blueprint中创建`GameplayAbilities`和`GameplayEffects`。

### 当前GAS存在的问题：
* `GameplayEffect`延迟调解（无法预测能力冷却，导致高延迟的玩家在低冷却能力的使用频率上低于低延迟玩家）。
* 无法预测`GameplayEffects`的移除。然而，我们可以预测添加具有相反效果的`GameplayEffects`以有效移除它们。这并不总是合适或可行，仍然是一个问题。
* 缺乏样板模板、多玩家示例和文档。希望这能有所帮助！

**[⬆ 返回顶部](#table-of-contents)**


<a name="sp"></a>
## 2. 示例项目

本节介绍一个多玩家第三人称射击示例项目，旨在帮助新手了解GameplayAbilitySystem插件，同时不要求对虚幻引擎完全陌生。用户应具备C++、Blueprint、UMG、复制（Replication）等中级知识。该项目展示了如何设置一个基本的多玩家第三人称射击项目，使用`AbilitySystemComponent`（`ASC`）在`PlayerState`类中为玩家/AI控制的英雄提供支持，同时在`Character`类中为AI控制的小兵提供`ASC`。

该项目的目标是保持简单，同时展示GAS基础知识，并演示一些常见能力，代码注释清晰。由于其针对初学者的特性，项目并未涵盖诸如[预测投射物](#concepts-p-spawn)等高级主题。

### 演示的概念

- `ASC`在`PlayerState`与`Character`上的应用
- 复制的`Attributes`
- 复制的动画蒙太奇
- `GameplayTags`
- 在`GameplayAbilities`内部及外部应用和移除`GameplayEffects`
- 根据护甲减少的伤害影响角色生命值
- `GameplayEffectExecutionCalculations`
- 击晕效果
- 死亡与重生
- 从服务器的能力中生成演员（投射物）
- 预测性地改变本地玩家的速度（如瞄准和冲刺）
- 持续消耗耐力以进行冲刺
- 使用法力施放能力
- 被动能力
- 堆叠`GameplayEffects`
- 目标演员
- 在Blueprint中创建的`GameplayAbilities`
- 在C++中创建的`GameplayAbilities`
- 每个`Actor`实例化的`GameplayAbilities`
- 非实例化的`GameplayAbilities`（如跳跃）
- 静态`GameplayCues`（火枪投射物撞击粒子效果）
- 演员`GameplayCues`（冲刺和击晕粒子效果）

### 英雄类能力

| 能力                    | 输入绑定          | 预测      | C++ / Blueprint | 描述                                                                                                                                                                  |
| ----------------------- | ----------------- | --------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 跳跃                    | 空格键            | 是        | C++             | 使英雄跳跃。                                                                                                                                                         |
| 射击                    | 左键              | 否        | C++             | 从英雄的枪中发射投射物。动画预测，但投射物不预测。                                                                                                               |
| 瞄准                    | 右键              | 是        | Blueprint       | 按住时，英雄会减速行走，摄像头缩放以便更精确地射击。                                                                                                             |
| 冲刺                    | 左Shift           | 是        | Blueprint       | 按住时，英雄会加速跑动，消耗耐力。                                                                                                                                 |
| 前冲                    | Q                 | 是        | Blueprint       | 英雄向前冲刺，消耗耐力。                                                                                                                                           |
| 被动护甲堆叠          | 被动              | 否        | Blueprint       | 每4秒获得一层护甲，最多4层。受到伤害时移除一层护甲。                                                                                                             |
| 陨石                    | R                 | 否        | Blueprint       | 玩家瞄准一个位置，对敌人投放陨石，造成伤害并击晕。目标预测，生成陨石不预测。                                                                                     |

无论`GameplayAbilities`是用C++还是Blueprint创建，两者混合使用以展示各自的实现方式。

小兵没有预定义的`GameplayAbilities`。红色小兵具有更高的生命恢复，而蓝色小兵则有更高的初始生命值。

在`GameplayAbility`命名中，使用后缀`_BP`表示该能力逻辑是在Blueprint中创建的；没有后缀则表示逻辑是在C++中创建的。

**Blueprint资产命名前缀**

| 前缀       | 资产类型            |
| ---------- | ------------------- |
| GA_        | GameplayAbility      |
| GC_        | GameplayCue          |
| GE_        | GameplayEffect       |

**[⬆ 返回顶部](#table-of-contents)**

<a name="setup"></a>
## 3. 使用GAS设置项目

设置项目以使用GAS的基本步骤：
1. 在编辑器中启用GameplayAbilitySystem插件。
2. 编辑`YourProjectName.Build.cs`，将`"GameplayAbilities", "GameplayTags", "GameplayTasks"`添加到`PrivateDependencyModuleNames`。
3. 刷新/重新生成Visual Studio项目文件。
4. 从4.24到5.2，必须调用`UAbilitySystemGlobals::Get().InitGlobalData()`以使用[`TargetData`](#concepts-targeting-data)。示例项目在`UAssetManager::StartInitialLoading()`中执行此操作。自5.3起，此调用自动进行。有关更多信息，请参见[`InitGlobalData()`](#concepts-asg-initglobaldata)。

完成上述步骤后，即可启用GAS。从这里开始，向`Character`或`PlayerState`添加`ASC`和`AttributeSet`，并开始创建[`GameplayAbilities`](#concepts-ga)和[`GameplayEffects`](#concepts-ge)！

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts"></a>
## 4. GAS概念

#### 章节

> 4.1 [能力系统组件](#concepts-asc)  
> 4.2 [游戏标签](#concepts-gt)  
> 4.3 [属性](#concepts-a)  
> 4.4 [属性集](#concepts-as)  
> 4.5 [游戏效果](#concepts-ge)  
> 4.6 [游戏能力](#concepts-ga)  
> 4.7 [能力任务](#concepts-at)  
> 4.8 [游戏提示](#concepts-gc)  
> 4.9 [能力系统全局变量](#concepts-asg)  
> 4.10 [预测](#concepts-p)  

<a name="concepts-asc"></a>
### 4.1 能力系统组件

`AbilitySystemComponent`（`ASC`）是GAS的核心。它是一个`UActorComponent`（[`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)），处理系统的所有交互。任何希望使用[`GameplayAbilities`](#concepts-ga)、拥有[`Attributes`](#concepts-a)或接收[`GameplayEffects`](#concepts-ge)的`Actor`都必须附加一个`ASC`。这些对象都存活于`ASC`内部，并由其管理和复制（`Attributes`通过其[`AttributeSet`](#concepts-as)进行复制）。开发者可以选择子类化此组件，但并非强制。

附加`ASC`的`Actor`被称为该`ASC`的`OwnerActor`。`ASC`的物理表示`Actor`称为`AvatarActor`。在简单的MOBA游戏中的AI小兵案例中，`OwnerActor`和`AvatarActor`可以是同一个`Actor`。在玩家控制的MOBA英雄中，`OwnerActor`是`PlayerState`，而`AvatarActor`是英雄的`Character`类。大多数`Actor`会将`ASC`附加在自身上。如果您的`Actor`将在重生时需要保留`Attributes`或`GameplayEffects`（例如MOBA中的英雄），则将`ASC`放在`PlayerState`上是理想选择。

**注意：** 如果您的`ASC`位于`PlayerState`上，则需要增加`PlayerState`的`NetUpdateFrequency`。默认情况下，`PlayerState`的该值很低，可能导致`Attributes`和`GameplayTags`等变化在客户端上产生延迟或感知延迟。确保启用[`自适应网络更新频率`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)，Fortnite使用了此功能。

如果`OwnerActor`和`AvatarActor`是不同的`Actors`，则两者都应该实现`IAbilitySystemInterface`。该接口有一个必须重写的函数`UAbilitySystemComponent* GetAbilitySystemComponent() const`，返回指向其`ASC`的指针。`ASC`通过查找该接口函数在系统内部相互交互。

`ASC`在`FActiveGameplayEffectsContainer ActiveGameplayEffects`中保存当前活动的`GameplayEffects`。

`ASC`在`FGameplayAbilitySpecContainer ActivatableAbilities`中保存其授予的`Gameplay Abilities`。每当您计划遍历`ActivatableAbilities.Items`时，请确保在循环的上方添加`ABILITYLIST_SCOPE_LOCK();`以锁定列表，防止由于移除能力而导致的更改。每个作用域内的`ABILITYLIST_SCOPE_LOCK();`会增加`AbilityScopeLockCount`，离开作用域时则减少。切勿在`ABILITYLIST_SCOPE_LOCK();`的作用域内尝试移除能力（清除能力的函数会在内部检查`AbilityScopeLockCount`，以防止在列表被锁定时移除能力）。

<a name="concepts-asc-rm"></a>
### 4.1.1 复制模式

`ASC`定义三种不同的复制模式，用于复制`GameplayEffects`、`GameplayTags`和`GameplayCues` - `Full`、`Mixed`和`Minimal`。`Attributes`通过其`AttributeSet`进行复制。

| 复制模式   | 使用场景                             | 描述                                                                                                                    |
| ---------- | ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `Full`     | 单人游戏                             | 每个`GameplayEffect`复制到每个客户端。                                                                                |
| `Mixed`    | 多人游戏，玩家控制的`Actors`       | `GameplayEffects`仅复制到拥有的客户端。只有`GameplayTags`和`GameplayCues`复制到所有人。                          |
| `Minimal`  | 多人游戏，AI控制的`Actors`         | 从不复制`GameplayEffects`。只有`GameplayTags`和`GameplayCues`复制到所有人。                                      |

**注意：** `Mixed`复制模式期望`OwnerActor`的`Owner`是`Controller`。`PlayerState`的`Owner`默认是`Controller`，但`Character`的则不是。如果在`OwnerActor`不是`PlayerState`的情况下使用`Mixed`复制模式，则需要在`OwnerActor`上调用`SetOwner()`并传入有效的`Controller`。

从4.24开始，`PossessedBy()`现在将`Pawn`的拥有者设置为新的`Controller`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-asc-setup"></a>
### 4.1.2 设置与初始化

`ASC`通常在`OwnerActor`的构造函数中创建，并明确标记为可复制。**这必须在C++中完成**。
```c++
AGDPlayerState::AGDPlayerState()
{
	// 创建能力系统组件，并设置为显式复制
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`ASC`需要在服务器和客户端上与其`OwnerActor`和`AvatarActor`进行初始化。您希望在`Pawn`的`Controller`设置后（即被控制后）进行初始化。单人游戏只需关注服务器路径。

对于玩家控制的角色，如果`ASC`位于`Pawn`上，通常在服务器端的`Pawn`的`PossessedBy()`函数中初始化，并在客户端的`PlayerController`的`AcknowledgePossession()`函数中初始化。


```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode 复制要求 ASC Owner 的 Owner 是 Controller。
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```
对于玩家控制的角色，如果`ASC`位于`PlayerState`上，通常在服务器端的`Pawn`的`PossessedBy()`函数中初始化，并在客户端的`Pawn`的`OnRep_PlayerState()`函数中初始化。这确保了`PlayerState`在客户端上存在。

```c++
// 仅限服务器
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// 在服务器上设置 ASC。客户端在 OnRep_PlayerState() 中执行此操作
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI 没有 PlayerController，因此我们可以在这里再次初始化以确保万无一失。对于拥有 PlayerController 的英雄，初始化两次不会有什么坏处。
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// 仅客户端  
void AGDHeroCharacter::OnRep_PlayerState(){  
    Super::OnRep_PlayerState();  
    AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();  
    if (PS) {  
        // 为客户端设置 ASC。服务器在 PossessedBy 中执行此操作。  
        AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());  
        // 初始化客户端的 ASC Actor 信息。服务器将在占有新 Actor 时初始化其 ASC。  
        AbilitySystemComponent->InitAbilityActorInfo(PS, this);  
    }  
    // ...  
}  
```

如果您收到错误信息 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`，则表示您未在客户端初始化您的`ASC`。

确保在客户端的`OnRep_PlayerState()`函数中正确设置并初始化`ASC`，以避免此类错误。

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-gt"></a>
### 4.2 游戏标签

[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html)是以`Parent.Child.Grandchild...`形式的层次名称，注册于`GameplayTagManager`。这些标签非常有用，用于对对象状态进行分类和描述。例如，如果角色被击晕，我们可以赋予它`State.Debuff.Stun`的`GameplayTag`，以表示击晕状态。

您会发现，许多以前用布尔值或枚举处理的逻辑现在可以用`GameplayTags`替代，您可以使用布尔逻辑判断对象是否拥有某些`GameplayTags`。

在给对象赋予标签时，我们通常将它们添加到其`ASC`中（如果存在），以便GAS能够与之交互。`UAbilitySystemComponent`实现了`IGameplayTagAssetInterface`，提供了访问其拥有的`GameplayTags`的函数。

多个`GameplayTags`可以存储在`FGameplayTagContainer`中。使用`GameplayTagContainer`优于使用`TArray<FGameplayTag>`，因为`GameplayTagContainers`提供了一些效率优化。如果启用项目设置中的`Fast Replication`，则可以高效地在`FGameplayTagContainers`中打包标签，以便复制。`Fast Replication`要求服务器和客户端拥有相同的`GameplayTags`列表。通常这不是问题，因此您应该启用此选项。`GameplayTagContainers`也可以返回`TArray<FGameplayTag>`以供迭代。

存储在`FGameplayTagCountContainer`中的`GameplayTags`具有一个`TagMap`，该地图存储该`GameplayTag`的实例数量。即使`FGameplayTagCountContainer`中仍可能包含`GameplayTag`，其`TagMapCount`也可能为零。在调试时，您可能会遇到这种情况，`ASC`仍然拥有某个`GameplayTag`。任何`HasTag()`或`HasMatchingTag()`等函数将检查`TagMapCount`，如果该`GameplayTag`不存在或其`TagMapCount`为零，则返回false。

`GameplayTags`必须提前在`DefaultGameplayTags.ini`中定义。虚幻引擎编辑器在项目设置中提供了一个接口，允许开发者管理`GameplayTags`，无需手动编辑`DefaultGameplayTags.ini`。`GameplayTag`编辑器可以创建、重命名、搜索引用和删除`GameplayTags`。

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

搜索`GameplayTag`引用将显示熟悉的`Reference Viewer`图形，展示所有引用该`GameplayTag`的资源。然而，这不会显示任何引用该`GameplayTag`的C++类。

重命名`GameplayTags`会创建一个重定向，以便仍引用原始`GameplayTag`的资产可以重定向到新的`GameplayTag`。如果可能，我更倾向于创建一个新的`GameplayTag`，手动更新所有引用到新的`GameplayTag`，然后删除旧的`GameplayTag`以避免创建重定向。

除了`Fast Replication`之外，`GameplayTag`编辑器还有一个选项，可以填充常见的复制`GameplayTags`以进一步优化它们。

从`GameplayEffect`中添加的`GameplayTags`会被复制。`ASC`允许您添加不被复制的`LooseGameplayTags`，这些标签必须手动管理。示例项目使用`LooseGameplayTag`表示`State.Dead`，以便拥有的客户端可以立即响应其健康值降至零的情况。重生时手动将`TagMapCount`重置为零。仅在处理`LooseGameplayTags`时手动调整`TagMapCount`。建议使用`UAbilitySystemComponent::AddLooseGameplayTag()`和`UAbilitySystemComponent::RemoveLooseGameplayTag()`函数，而不是手动调整`TagMapCount`。

获取`GameplayTag`的C++引用：

```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

对于高级的`GameplayTag`操作，例如获取父级或子级`GameplayTags`，请查看`GameplayTagManager`提供的函数。要访问`GameplayTagManager`，请包含`GameplayTagManager.h`并使用`UGameplayTagManager::Get().FunctionName`进行调用。`GameplayTagManager`将`GameplayTags`存储为关系节点（父级、子级等），以便于比常量字符串操作和比较更快的处理。

`GameplayTags`和`GameplayTagContainers`可以具有可选的`UPROPERTY`修饰符`Meta = (Categories = "GameplayCue")`，这可以在蓝图中过滤标签，仅显示父标签为`GameplayCue`的`GameplayTags`。当您知道`GameplayTag`或`GameplayTagContainer`变量应仅用于`GameplayCues`时，这非常有用。

另外，还有一个名为`FGameplayCueTag`的单独结构，封装了`FGameplayTag`，并自动过滤蓝图中的`GameplayTags`，仅显示父标签为`GameplayCue`的标签。

如果您想在函数中过滤`GameplayTag`参数，请使用`UFUNCTION`修饰符`Meta = (GameplayTagFilter = "GameplayCue")`。函数中的`GameplayTagContainer`参数无法过滤。如果您希望修改引擎以允许此操作，请查看`Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp`中`SGameplayTagGraphPin::ParseDefaultValueData()`如何调用`FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);`并将`FilterString`传递给`SGameplayTagWidget`。`Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp`中的这些函数的`GameplayTagContainer`版本不检查元字段属性，而是直接传递过滤器。

示例项目广泛使用`GameplayTags`。

**[⬆ 返回顶部](#table-of-contents)**


 

<a name="concepts-gt-change"></a>
### 4.2.1 响应游戏标签的变化

`ASC`提供了一个委托，用于当`GameplayTags`被添加或移除时触发。它接受一个`EGameplayTagEventType`，可以指定仅在`GameplayTag`被添加/移除时触发，或者在`GameplayTag`的`TagMapCount`发生任何变化时触发。

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

回调函数有一个参数，表示`GameplayTag`和新的`TagCount`：

```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gt-loadfromplugin"></a>
### 4.2.2 从插件 .ini 文件加载游戏标签

如果您创建了一个具有自己 .ini 文件的插件，并在其中定义了`GameplayTags`，您可以在插件的`StartupModule()`函数中加载该插件的`GameplayTag` .ini 目录。

例如，以下是虚幻引擎自带的CommonConversation插件如何实现的：

```c++
void FCommonConversationRuntimeModule::StartupModule()
{
    TSharedPtr<IPlugin> ThisPlugin = IPluginManager::Get().FindPlugin(TEXT("CommonConversation"));
    check(ThisPlugin.IsValid());
    
    UGameplayTagsManager::Get().AddTagIniSearchPath(ThisPlugin->GetBaseDir() / TEXT("Config") / TEXT("Tags"));

    //...
}
```

这将在引擎启动时查找目录`Plugins\CommonConversation\Config\Tags`，并加载其中的任何包含`GameplayTags`的 .ini 文件（如果插件已启用）。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 属性

<a name="concepts-a-definition"></a>
#### 4.3.1 属性定义

`Attributes`是由结构 [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html) 定义的浮点值。这些值可以表示从角色的健康值到角色的等级，甚至是药水的充能次数。如果它是属于`Actor`的游戏相关数值，您应考虑使用`Attribute`。`Attributes`通常仅应通过 [`GameplayEffects`](#concepts-ge) 修改，以便ASC能够 [预测](#concepts-p) 更改。

`Attributes`由 [`AttributeSet`](#concepts-as) 定义并存储。`AttributeSet`负责复制被标记为复制的`Attributes`。有关如何定义`Attributes`的信息，请参见 [`AttributeSets`](#concepts-as) 部分。

**提示：** 如果您不希望某个`Attribute`出现在编辑器的属性列表中，可以使用 `Meta = (HideInDetailsView)` 属性修饰符。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 基础值与当前值

一个`Attribute`由两个值组成：`BaseValue`和`CurrentValue`。`BaseValue`是`Attribute`的永久值，而`CurrentValue`是`BaseValue`加上来自`GameplayEffects`的临时修改。例如，您的角色可能有一个移动速度`Attribute`，其`BaseValue`为600单位/秒。由于尚未有任何`GameplayEffects`修改移动速度，因此`CurrentValue`也是600 u/s。如果她获得一个临时的50 u/s移动速度增益，则`BaseValue`保持不变为600 u/s，而`CurrentValue`现在为600 + 50，总共为650 u/s。当移动速度增益结束时，`CurrentValue`会恢复到600 u/s的`BaseValue`。

初学者常常会混淆`BaseValue`与`Attribute`的最大值，并试图将其视为最大值。这是一种错误的做法。可以变化或在能力或用户界面中引用的`Attributes`的最大值应被视为单独的`Attributes`。对于硬编码的最大值和最小值，有一种方法可以定义一个`DataTable`，其中包含`FAttributeMetaData`，用于设置最大值和最小值，但Epic在该结构上方的注释称其为“进行中的工作”。有关更多信息，请参见`AttributeSet.h`。为了避免混淆，我建议将可以在能力或用户界面中引用的最大值作为单独的`Attributes`来定义，而硬编码的最大值和最小值仅用于限制`Attributes`，应在`AttributeSet`中定义为硬编码的浮点数。`Attributes`的限制在 [PreAttributeChange()](#concepts-as-preattributechange) 中讨论，以处理对`CurrentValue`的更改，并在 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute) 中讨论，以处理来自`GameplayEffects`的`BaseValue`更改。

`BaseValue`的永久性更改来自`Instant` `GameplayEffects`，而`Duration`和`Infinite` `GameplayEffects`则更改`CurrentValue`。周期性`GameplayEffects`被视为瞬时`GameplayEffects`并更改`BaseValue`。

**[⬆ 返回顶部](#table-of-contents)**



<a name="concepts-a-meta"></a>
#### 4.3.3 元属性

某些`Attributes`被视为临时值的占位符，用于与其他`Attributes`交互。这些被称为`Meta Attributes`。例如，我们通常将伤害定义为`Meta Attribute`。而不是直接通过`GameplayEffect`改变我们的健康`Attribute`，我们使用一个名为伤害的`Meta Attribute`作为占位符。这样，伤害值可以在[`GameplayEffectExecutionCalculation`](#concepts-ge-ec)中通过增益和减益进行修改，并且可以在`AttributeSet`中进一步处理，例如从当前护盾`Attribute`中减去伤害，最后从健康`Attribute`中减去剩余值。伤害的`Meta Attribute`在`GameplayEffects`之间没有持久性且被每个效果覆盖。`Meta Attributes`通常不被复制。

`Meta Attributes`为伤害和治疗等事物提供了良好的逻辑分离，区分了“我们造成了多少伤害？”和“我们如何处理这些伤害？”。这种逻辑分离意味着我们的`Gameplay Effects`和`Execution Calculations`不需要了解目标如何处理伤害。继续以伤害为例，`Gameplay Effect`决定了伤害的多少，而`AttributeSet`则决定如何处理该伤害。并非所有角色都具有相同的`Attributes`，尤其是在使用子类化的`AttributeSets`时。基础`AttributeSet`类可能只有一个健康`Attribute`，但子类化的`AttributeSet`可能会增加一个护盾`Attribute`。具有护盾`Attribute`的子类化`AttributeSet`会以不同于基础`AttributeSet`类的方式分配接收到的伤害。

虽然`Meta Attributes`是一种良好的设计模式，但并不是强制性的。如果您只使用一个`Execution Calculation`处理所有伤害实例，并且所有角色共享一个`Attribute Set`类，那么您可以在`Execution Calculation`中直接处理对健康、护盾等的伤害分配。这将牺牲灵活性，但这可能对您来说是可以接受的。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 响应属性变化

要监听`Attribute`变化以更新UI或其他游戏元素，使用`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`。此函数返回一个委托，您可以绑定到该委托上，它将在`Attribute`变化时自动调用。该委托提供`FOnAttributeChangeData`参数，其中包含`NewValue`、`OldValue`和`FGameplayEffectModCallbackData`。
**注意：** `FGameplayEffectModCallbackData`仅在服务器上设置。

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

示例项目在`GDPlayerState`上绑定了`Attribute`值变化的委托，以更新HUD并在健康值降至零时响应玩家死亡。

示例项目中包括一个将此封装为`ASyncTask`的自定义蓝图节点。它在`UI_HUD` UMG Widget中用于更新健康、魔法和耐力值。该`AsyncTask`将一直存在，直到手动调用`EndTask()`，我们在UMG Widget的`Destruct`事件中执行此操作。请参见`AsyncTaskAttributeChanged.h/cpp`。
![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**![监听属性变化的BP节点][]**[⬆ 返回顶部](#table-of-contents)**
<a name="concepts-a-derived"></a>
#### 4.3.5 派生属性
要创建一个值部分或完全源自一个或多个其他`Attributes`的`Attribute`，使用一个具有一个或多个基于`Attribute`或[`MMC`](#concepts-ge-mmc)的`Modifiers`的`Infinite` `GameplayEffect`。`Derived Attribute`将在其所依赖的`Attribute`更新时自动更新。
`Derived Attribute`的所有`Modifiers`的最终公式与`Modifier Aggregators`的公式相同。如果需要以特定顺序进行计算，请在`MMC`内部完成所有操作。

```c++
((CurrentValue + Additive) * Multiplicitive) / Division
```

**注意：** 如果在PIE中与多个客户端一起游戏，您需要在编辑器首选项中禁用`Run Under One Process`，否则`Derived Attributes`在其他客户端更新其独立的`Attributes`时将不会更新。在这个例子中，我们有一个`Infinite` `GameplayEffect`，其`TestAttrA`的值从`Attributes` `TestAttrB`和`TestAttrC`派生，公式为`TestAttrA = (TestAttrA + TestAttrB) * (2 * TestAttrC)`。每当`Attributes`更新其值时，`TestAttrA`会自动重新计算其值。

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 属性集

<a name="concepts-as-definition"></a>
#### 4.4.1 属性集定义
`AttributeSet`定义、持有并管理对`Attributes`的更改。开发者应该从 [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html) 子类化。在`OwnerActor`的构造函数中创建`AttributeSet`会自动将其注册到其`ASC`中。**这必须在C++中完成**。

**[⬆ 返回顶部](#table-of-contents)**
<a name="concepts-as-design"></a>
#### 4.4.2 属性集设计
一个`ASC`可以有一个或多个`AttributeSets`。`AttributeSets`的内存开销微乎其微，因此使用多少个`AttributeSets`是开发者的组织决策。

可以有一个大型的整体`AttributeSet`由游戏中的每个`Actor`共享，仅在需要时使用属性，同时忽略未使用的属性。

或者，您也可以选择有多个`AttributeSet`，表示一组`Attributes`，根据需要选择性地将其添加到您的`Actors`中。例如，您可以有一个用于健康相关`Attributes`的`AttributeSet`，一个用于魔法相关`Attributes`的`AttributeSet`，等等。在MOBA游戏中，英雄可能需要魔法，但小兵可能不需要。因此，英雄会获得魔法`AttributeSet`，而小兵则不会。

此外，`AttributeSets`还可以通过子类化作为选择性决定`Actor`具有哪些`Attributes`的另一种方式。`Attributes`在内部被称为`AttributeSetClassName.AttributeName`。当您子类化一个`AttributeSet`时，父类中的所有`Attributes`仍将具有父类名称作为前缀。

尽管您可以有多个`AttributeSet`，但在一个`ASC`上不应有多个相同类的`AttributeSet`。如果您有多个来自同一类的`AttributeSet`，系统将不知道使用哪个`AttributeSet`，并只会选择一个。


<a name="concepts-as-design"></a>
#### 4.4.2 属性集设计
一个 `ASC` 可以拥有一个或多个 `AttributeSets`。属性集的内存开销微乎其微，因此使用多少个 `AttributeSets` 是开发者的组织决策。

可以接受在游戏中的每个 `Actor` 共享一个大型的单体 `AttributeSet`，并仅在需要时使用属性，同时忽略未使用的属性。

另外，您可以选择拥有多个 `AttributeSet`，代表一组您根据需要选择性添加到 `Actors` 的属性。例如，您可以拥有一个与健康相关的 `AttributeSet`，一个与法力相关的 `AttributeSet`，等等。在 MOBA 游戏中，英雄可能需要法力，而小兵则不需要。因此，英雄会获取法力 `AttributeSet`，而小兵则不会。

此外，`AttributeSets` 可以作为子类化的另一种手段，选择性地决定一个 `Actor` 拥有哪些 `Attributes`。属性在内部被称为 `AttributeSetClassName.AttributeName`。当您对子类化一个 `AttributeSet` 时，父类的所有属性仍将以父类的名称作为前缀。

虽然您可以拥有多个 `AttributeSet`，但在一个 `ASC` 上不应有超过一个相同类的 `AttributeSet`。如果您有来自同一类的多个 `AttributeSet`，它将不知道使用哪个 `AttributeSet`，并且只会选择其中一个。

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 具有单独属性的子组件
在 `Pawn` 上有多个可损坏组件的场景中，例如单独可损坏的护甲部件，我建议如果您知道 `Pawn` 可能有的最大可损坏组件数量，则在一个 `AttributeSet` 上创建那么多健康属性 - DamageableCompHealth0、DamageableCompHealth1 等，以代表这些可损坏组件的逻辑“槽”。在您的可损坏组件类实例中，分配可以被 `GameplayAbilities` 或 [`Executions`](#concepts-ge-ec) 读取的槽号属性，以了解应对哪个属性施加伤害。具有少于最大数量或零个可损坏组件的 `Pawns` 也是可以的。仅仅因为一个 `AttributeSet` 有一个属性，并不意味着您必须使用它。未使用的属性占用的内存微不足道。

如果您的子组件每个需要多个`Attributes`，存在潜在无限数量的子组件，子组件可以被其他玩家拆分并使用（例如武器），或者出于任何其他原因这一方法不适合您，我建议您放弃使用`Attributes`，而是在组件上存储普通的浮点数。请参见 [Item Attributes](#concepts-as-design-itemattributes)。

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 在运行时添加和移除 AttributeSets
可以在运行时从 `ASC` 中添加和移除 `AttributeSets`；然而，移除 `AttributeSets` 可能是危险的。例如，如果在客户端移除一个 `AttributeSet` 后，服务器上的属性值变化被复制到客户端，那么该属性将找不到其 `AttributeSet`，从而导致游戏崩溃。

在武器添加到库存时：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

在武器从库存中移除时：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 物品属性（武器弹药）
实现可装备物品（武器弹药、护甲耐久度等）的几种方法都是将值直接存储在物品上。这对于在其生命周期内可以被多个玩家装备的物品是必要的。

> 1. 在物品上使用普通的浮点数（**推荐**）
> 1. 在物品上使用单独的 `AttributeSet`
> 1. 在物品上使用单独的 `ASC`

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 物品上的普通浮点数
与其使用属性，不如在物品类实例上存储普通的浮点值。Fortnite 和 [GASShooter](https://github.com/tranek/GASShooter) 以这种方式处理枪械弹药。对于枪械，将最大弹匣容量、当前弹匣中的弹药、备用弹药等直接作为复制的浮点数（`COND_OwnerOnly`）存储在枪械实例上。如果武器共享备用弹药，您可以将备用弹药移动到角色上，作为共享弹药 `AttributeSet` 中的一个属性（重新装填能力可以使用 `Cost GE` 从备用弹药中提取到枪械的浮点弹药中）。由于您不使用属性来表示当前弹匣中的弹药，因此需要在 `UGameplayAbility` 中重写一些函数，以检查并根据枪械上的浮点数应用成本。在授予能力时，将枪械作为 [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec) 的 `SourceObject`，意味着您将在能力内部访问授予能力的枪械。

为了防止枪械在自动射击期间将弹药数量复制回去并覆盖本地弹药数量，请在 `PreReplication()` 中禁用复制，同时玩家具有 `IsFiring` `GameplayTag`。您实际上是在这里进行自己的本地预测。

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

优点：
1. 避免了使用 `AttributeSets` 的限制（见下文）

限制：
1. 无法使用现有的 `GameplayEffect` 工作流（例如弹药使用的 `Cost GEs` 等）
1. 需要重写 `UGameplayAbility` 上的关键函数，以检查并将弹药成本应用于枪械的浮点数



<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 `AttributeSet` 在物品上
在物品上使用单独的 `AttributeSet`，在将其添加到玩家的库存时添加到玩家的 `ASC`，可以工作，但有一些主要的限制。我在早期版本的 [GASShooter](https://github.com/tranek/GASShooter) 中实现了这一点，用于武器弹药。武器将其属性（如最大弹匣容量、当前弹药、备用弹药等）存储在一个属于武器类的 `AttributeSet` 中。如果武器共享备用弹药，则将备用弹药移动到角色的共享弹药 `AttributeSet` 中。当武器在服务器上添加到玩家的库存时，武器会将其 `AttributeSet` 添加到玩家的 `ASC::SpawnedAttributes` 中。然后服务器将此信息复制到客户端。如果武器从库存中移除，则会从 `ASC::SpawnedAttributes` 中移除其 `AttributeSet`。

当 `AttributeSet` 存在于 `OwnerActor` 以外的物体（例如武器）上时，您最初会在 `AttributeSet` 中遇到一些编译错误。解决方法是在 `BeginPlay()` 中构造 `AttributeSet`，而不是在构造函数中，并在武器上实现 `IAbilitySystemInterface`（在将武器添加到玩家库存时设置指向 `ASC` 的指针）。

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

您可以通过查看这个 [GASShooter 的旧版本](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735) 来实践这一点。

优点：
1. 可以使用现有的 `GameplayAbility` 和 `GameplayEffect` 工作流（用于弹药使用的 `Cost GEs` 等）
2. 对于非常少量的物品设置简单

限制：
1. 每种武器类型必须创建一个新的 `AttributeSet` 类。`ASC` 实际上只能拥有一个 `AttributeSet` 实例，因为对属性的更改会在 `ASC` 的 `SpawnedAttributes` 数组中查找第一个实例。相同 `AttributeSet` 类的额外实例将被忽略。
2. 由于上述原因，玩家的库存中只能有一种类型的武器。
3. 移除 `AttributeSet` 是危险的。在 GASShooter 中，如果玩家因火箭自杀，玩家将立即从其库存中移除火箭发射器（包括从 `ASC` 中移除其 `AttributeSet`）。当服务器复制火箭发射器的弹药 `Attribute` 发生变化时，客户端的 `ASC` 上不再存在该 `AttributeSet`，从而导致游戏崩溃。

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 `ASC` 在物品上
在每个物品上放置一个完整的 `AbilitySystemComponent` 是一种极端的方法。我个人没有这样做过，也没有见过这种情况。这需要大量的工程工作才能使其正常运行。

> 能否在同一个拥有者下拥有多个 AbilitySystemComponents，但具有不同的化身（例如在 `pawn` 和武器/物品/弹丸上，所有者设置为 `PlayerState`）？
> 
> 我看到的第一个问题是在拥有者的 Actor 上实现 `IGameplayTagAssetInterface` 和 `IAbilitySystemInterface`。前者可能是可以的：只需从所有的 `ASC` 中聚合标签（但要小心 - `HasAllMatchingGameplayTags` 可能只能通过跨 `ASC` 聚合满足。仅仅将调用转发到每个 `ASC` 并将结果进行或运算是不够的）。但后者更棘手：哪个 `ASC` 是权威的？如果有人想应用一个 GE，应该哪个接收它？也许您可以解决这些问题，但这一方面的问题将是最难的：拥有者将有多个 `ASC`。

尽管如此，在 `pawn` 和武器上分开使用 `ASC` 是有意义的。例如，区分描述武器的标签与描述拥有 `pawn` 的标签。也许武器授予的标签也“适用于”拥有者而不是其他任何对象（例如，属性和 GE 是独立的，但拥有者将聚合所拥有的标签，如上所述）。我相信这可以正常工作。但在同一拥有者下拥有多个 `ASC` 可能会变得复杂。

*Dave Ratti 来自 Epic 对 [社区问题 #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 的回答*

优点：
1. 可以使用现有的 `GameplayAbility` 和 `GameplayEffect` 工作流（用于弹药使用的 `Cost GEs` 等）
2. 可以重用 `AttributeSet` 类（每个武器的 `ASC` 上都有一个）

限制：
1. 工程成本未知
2. 这是否可行？

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 定义属性
**`Attributes` 只能在 C++ 中定义**，在 `AttributeSet` 的头文件中。建议在每个 `AttributeSet` 头文件的顶部添加此宏块。它将自动生成您的 `Attributes` 的获取和设置函数。

```c++
// 使用来自 AttributeSet.h 的宏
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

一个被复制的生命值属性将这样定义：

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

还需在头文件中定义 `OnRep` 函数：
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet` 的 `.cpp` 文件应使用预测系统的 `GAMEPLAYATTRIBUTE_REPNOTIFY` 宏填充 `OnRep` 函数：
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最后，需要将 `Attribute` 添加到 `GetLifetimeReplicatedProps`：
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always` 指示 `OnRep` 函数在本地值已经等于从服务器复制下来的值时触发（由于预测）。默认情况下，如果本地值与从服务器复制下来的值相同，则不会触发 `OnRep` 函数。

如果 `Attribute` 不是像 `Meta Attribute` 那样被复制，则可以跳过 `OnRep` 和 `GetLifetimeReplicatedProps` 步骤。

**[⬆ 返回顶部](#table-of-contents)**



<a name="concepts-as-init"></a>
#### 4.4.4 初始化属性
初始化 `Attributes`（设置其 `BaseValue` 并因此将其 `CurrentValue` 设置为某个初始值）有多种方法。Epic 建议使用即时 `GameplayEffect`。这是示例项目中使用的方法。

请查看示例项目中的 `GE_HeroAttributes` Blueprint，了解如何创建一个即时 `GameplayEffect` 来初始化 `Attributes`。此 `GameplayEffect` 的应用在 C++ 中进行。

如果在定义 `Attributes` 时使用了 `ATTRIBUTE_ACCESSORS` 宏，将自动为每个 `Attribute` 在 `AttributeSet` 上生成一个初始化函数，可以在 C++ 中随时调用。

```c++
// InitHealth(float InitialValue) 是为定义为 `ATTRIBUTE_ACCESSORS` 宏的属性 'Health' 自动生成的函数
AttributeSet->InitHealth(100.0f);
```

有关更多初始化 `Attributes` 的方法，请参见 `AttributeSet.h`。

**注意：** 在 4.24 之前，`FAttributeSetInitterDiscreteLevels` 不支持 `FGameplayAttributeData`。它是在属性为原始浮点数时创建的，并会抱怨 `FGameplayAttributeData` 不是 `Plain Old Data`（`POD`）。这一问题在 4.24 中已修复 [UE-76557](https://issues.unrealengine.com/issue/UE-76557)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)` 是 `AttributeSet` 中的主要函数之一，用于在 `Attribute` 的 `CurrentValue` 发生变化之前响应变化。它是通过引用参数 `NewValue` 来限制传入变化的理想位置。

例如，示例项目中通过以下方式限制移动速度修饰符：
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// 速度不能低于 150 单位/秒，不能高于 1000 单位/秒
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
`GetMoveSpeedAttribute()` 函数是通过我们在 `AttributeSet.h` 中添加的宏块创建的（[定义属性](#concepts-as-attributes)）。

此函数在任何 `Attributes` 发生变化时触发，无论是使用通过宏块在 `AttributeSet.h` 中定义的 `Attribute` 设置器，还是使用 [`GameplayEffects`](#concepts-ge)。

**注意：** 此处的任何限制不会永久改变 `ASC` 上的修饰符。它仅更改从查询修饰符返回的值。这意味着任何通过 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 和 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) 从所有修饰符计算 `CurrentValue` 的操作需要再次实现限制。

**注意：** Epic 对 `PreAttributeChange()` 的评论表示，建议不要将其用于游戏事件，而应主要用于限制。推荐的 `Attribute` 变化游戏事件处理位置是 `UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`（[响应属性变化](#concepts-a-changes)）。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)` 仅在通过即时 [`GameplayEffect`](#concepts-ge) 更改 `Attribute` 的 `BaseValue` 后触发。这是进行更多 `Attribute` 操作的有效位置。

例如，在示例项目中，我们在这里从健康 `Attribute` 中减去最终伤害的 `Meta Attribute`。如果有一个护盾 `Attribute`，我们将首先从护盾中减去伤害，然后再从健康中减去剩余部分。示例项目还使用此位置来应用击中反应动画，显示浮动伤害数字，并将经验和金钱奖励分配给击杀者。设计上，伤害 `Meta Attribute` 将始终通过即时 `GameplayEffect` 传递，而不会通过 `Attribute` 设置器。

其他将仅通过即时 `GameplayEffects` 更改其 `BaseValue` 的 `Attributes`（如法力和耐力）也可以在这里限制为其最大值的对应 `Attributes`。

**注意：** 当调用 `PostGameplayEffectExecute()` 时，`Attribute` 的更改已经发生，但尚未复制回客户端，因此在这里限制值不会导致两次网络更新。客户端仅在限制后收到更新。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)` 在为该集合中的 `Attribute` 创建 `Aggregator` 时触发。它允许自定义 [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html) 的设置。`AggregatorEvaluateMetaData` 被 `Aggregator` 用于根据应用于其上的所有 [`Modifiers`](#concepts-ge-mods) 评估 `Attribute` 的 `CurrentValue`。默认情况下，`AggregatorEvaluateMetaData` 仅被 `Aggregator` 用于确定哪些 `Modifiers` 适用，例如 `MostNegativeMod_AllPositiveMods`，它允许所有正 `Modifiers`，但限制负 `Modifiers` 仅为最负的一个。Paragon 使用这一点来确保即使在玩家身上有多个减速效果时，也只允许最负的移动速度减速效果应用。

要在仅允许最负的 `Modifier` 和所有正 `Modifiers` 的情况下使用 `AggregatorEvaluateMetaData`，可以这样实现：

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

您的自定义 `AggregatorEvaluateMetaData` 资格应作为静态变量添加到 `FAggregatorEvaluateMetaDataLibrary`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 游戏玩法效果

<a name="concepts-ge-definition"></a>
#### 4.5.1 游戏玩法效果定义
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) (`GE`) 是能力改变 [`Attributes`](#concepts-a) 和 [`GameplayTags`](#concepts-gt) 的载体。它们可以造成立即的 `Attribute` 变化，如伤害或治疗，或施加长期的状态增益/减益，如移动速度提升或眩晕。`UGameplayEffect` 类旨在作为一种 **数据-only** 类，定义单一的游戏玩法效果。游戏玩法效果中不应添加额外逻辑。通常设计师会创建许多 `UGameplayEffect` 的 Blueprint 子类。

`GameplayEffects` 通过 [`Modifiers`](#concepts-ge-mods) 和 [`Executions`（`GameplayEffectExecutionCalculation`）](#concepts-ge-ec) 更改 `Attributes`。

`GameplayEffects` 有三种持续时间类型：`Instant`、`Duration` 和 `Infinite`。

此外，`GameplayEffects` 可以添加/执行 [`GameplayCues`](#concepts-gc)。即时 `GameplayEffect` 将在 `GameplayCue` `GameplayTags` 上调用 `Execute`，而 `Duration` 或 `Infinite` `GameplayEffect` 将在 `GameplayCue` `GameplayTags` 上调用 `Add` 和 `Remove`。

| 持续时间类型 | GameplayCue 事件 | 使用时机                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | 对 `Attribute` 的 `BaseValue` 进行立即的永久性更改。`GameplayTags` 不会被应用，甚至不会存在一帧。                                                                                                                                    |
| `Duration`    | Add & Remove      | 对 `Attribute` 的 `CurrentValue` 进行临时更改，并应用将在 `GameplayEffect` 过期或手动移除时被移除的 `GameplayTags`。持续时间在 `UGameplayEffect` 类/Blueprint 中指定。                                                             |
| `Infinite`    | Add & Remove      | 对 `Attribute` 的 `CurrentValue` 进行临时更改，并应用将在 `GameplayEffect` 被移除时被移除的 `GameplayTags`。这些效果永远不会自行过期，必须通过能力或 `ASC` 手动移除。                                                           |

`Duration` 和 `Infinite` `GameplayEffects` 可以在应用后根据其 `Period` 每 `X` 秒应用其 `Modifiers` 和 `Executions`，这称为 `Periodic Effects`。在更改 `Attribute` 的 `BaseValue` 和执行 `GameplayCues` 时，`Periodic Effects` 被视为 `Instant` `GameplayEffects`。这些对于持续伤害（DOT）类型效果非常有用。**注意：** `Periodic Effects` 不能被 [预测](#concepts-p)。

`Duration` 和 `Infinite` `GameplayEffects` 可以在应用后根据其 `Ongoing Tag Requirements` 的满足与否临时开启和关闭。如果关闭一个 `GameplayEffect`，则会移除其 `Modifiers` 和应用的 `GameplayTags`，但不会移除 `GameplayEffect`。重新开启 `GameplayEffect` 将重新应用其 `Modifiers` 和 `GameplayTags`。

如果您需要手动重新计算 `Duration` 或 `Infinite` `GameplayEffect` 的 `Modifiers`（例如，您有一个 `MMC` 使用不来自 `Attributes` 的数据），可以调用 `UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`，使用与其已有的相同级别 `UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`。基于后端 `Attributes` 的修饰符在这些属性更新时会自动更新。`SetActiveGameplayEffectLevel()` 更新 `Modifiers` 的关键函数包括：

```c++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// 私有函数，否则我们在不需要将级别设置为相同的情况下调用这三个函数
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects` 通常不会被实例化。当能力或 `ASC` 想要应用 `GameplayEffect` 时，它会从 `GameplayEffect` 的 `ClassDefaultObject` 创建一个 [`GameplayEffectSpec`](#concepts-ge-spec)。成功应用的 `GameplayEffectSpecs` 然后被添加到一个名为 `FActiveGameplayEffect` 的新结构中，这是 `ASC` 在一个特殊的容器结构 `ActiveGameplayEffects` 中跟踪的。

**[⬆ 返回顶部](#table-of-contents)**






<a name="concepts-ge-applying"></a>
#### 4.5.2 应用Gameplay Effects
`GameplayEffects` 可以通过许多方式应用，通常使用 [`GameplayAbilities`](#concepts-ga) 和 `ASC` 中的函数，这些函数通常以 `ApplyGameplayEffectTo` 的形式存在。这些不同的函数本质上是一些便捷方法，最终会调用 `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()` 来作用于目标(`Target`)。

要在 `GameplayAbility` 之外应用 `GameplayEffects`，例如从一个投射物中，你需要获取目标(`Target`)的 `ASC`，然后使用其函数之一来调用 `ApplyGameplayEffectToSelf`。

你可以通过绑定其委托来监听任何 `Duration`（持续时间型）或 `Infinite`（无限时长型）`GameplayEffects` 何时被添加到一个 `ASC` 上：
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
回调函数：
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

服务器无论在何种复制模式下都会调用该函数。自主代理仅在 `Full` 和 `Mixed` 复制模式下为复制的 `GameplayEffects` 调用此函数。模拟代理仅在 `Full` [复制模式](#concepts-asc-rm)下调用此函数。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 移除Gameplay Effects
`GameplayEffects` 可以通过许多方式移除，通常使用 [`GameplayAbilities`](#concepts-ga) 和 `ASC` 中的函数，这些函数通常以 `RemoveActiveGameplayEffect` 的形式存在。这些不同的函数本质上是一些便捷方法，最终会调用 `FActiveGameplayEffectsContainer::RemoveActiveEffects()` 来作用于目标(`Target`)。

要在 `GameplayAbility` 之外移除 `GameplayEffects`，需要获取目标(`Target`)的 `ASC`，并使用其函数之一来调用 `RemoveActiveGameplayEffect`。

你可以通过绑定其委托来监听任何 `Duration`（持续时间型）或 `Infinite`（无限时长型）`GameplayEffects` 何时从 `ASC` 中移除：
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
回调函数：
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

服务器无论在何种复制模式下都会调用该函数。自主代理仅在 `Full` 和 `Mixed` 复制模式下为复制的 `GameplayEffects` 调用此函数。模拟代理仅在 `Full` [复制模式](#concepts-asc-rm)下调用此函数。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 Gameplay Effect 修饰符
`修饰符` 用于更改一个 `Attribute`，是唯一可以[预测性](#concepts-p)更改 `Attribute` 的方式。一个 `GameplayEffect` 可以包含零个或多个 `修饰符`。每个 `修饰符` 负责通过指定的操作更改一个 `Attribute`。

| 操作        | 描述                                                                 |
| ----------- | -------------------------------------------------------------------- |
| `Add`       | 将结果添加到 `修饰符` 指定的 `Attribute` 中。使用负值表示减法。     |
| `Multiply`  | 将结果乘以 `修饰符` 指定的 `Attribute`。                              |
| `Divide`    | 将结果除以 `修饰符` 指定的 `Attribute`。                              |
| `Override`  | 使用结果覆盖 `修饰符` 指定的 `Attribute`。                            |

一个 `Attribute` 的 `CurrentValue` 是其所有 `修饰符` 的聚合结果加上其 `BaseValue`。`修饰符` 的聚合公式在 `GameplayEffectAggregator.cpp` 中的 `FAggregatorModChannel::EvaluateWithBase` 定义：
```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

任何 `Override` 类型的 `修饰符` 将覆盖最终值，以最后应用的 `修饰符` 为准。

**注意：** 对于基于百分比的变化，请务必使用 `Multiply` 操作，以确保其发生在加法之后。

**注意：** [预测性](#concepts-p)对于百分比变化可能存在问题。

`修饰符` 有四种类型：可扩展浮点、基于属性、自定义计算类以及调用者设置的值。这些类型都会生成某个浮点值，然后通过其操作更改 `修饰符` 指定的 `Attribute`。

| `修饰符` 类型               | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats` 是一种结构，可以指向一个数据表，其中包含以变量为行、等级为列的值。`Scalable Floats` 会自动读取能力当前等级（或在 [`GameplayEffectSpec`](#concepts-ge-spec) 上被覆盖的等级）的指定表行值。此值还可以通过一个系数进一步调整。如果未指定数据表/行，则将值视为 1，系数可用于为所有等级硬编码单一值。     ![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `Attribute Based`          | `Attribute Based` `修饰符` 使用在 `Source`（创建 `GameplayEffectSpec` 的对象）或 `Target`（接收 `GameplayEffectSpec` 的对象）上的某个基础 `Attribute` 的 `CurrentValue` 或 `BaseValue`，然后通过一个系数以及系数之前和之后的加法来进一步调整。`快照` 表示在创建 `GameplayEffectSpec` 时捕获基础 `Attribute`，而非快照表示在应用 `GameplayEffectSpec` 时捕获。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `Custom Calculation Class` | `Custom Calculation Class` 提供了复杂 `修饰符` 的最大灵活性。此 `修饰符` 使用一个 [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) 类，还可以通过一个系数以及系数之前和之后的加法来进一步调整生成的浮点值。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `Set By Caller`            | `SetByCaller` `修饰符` 是一种在运行时由能力或创建 `GameplayEffectSpec` 的实体在 `GameplayEffectSpec` 上设置的值。例如，如果你希望根据玩家按下按钮的时间长短来设置伤害值，则可以使用 `SetByCaller`。`SetByCallers` 实质上是 `GameplayEffectSpec` 上的 `TMap<FGameplayTag, float>`。`修饰符` 仅仅是告诉 `Aggregator` 查找与提供的 `GameplayTag` 关联的 `SetByCaller` 值。`修饰符` 设置为 `SetByCaller` 但 `GameplayEffectSpec` 上没有正确的 `GameplayTag` 的情况下，游戏会抛出运行时错误并返回值为 0。这可能会在 `Divide` 操作的情况下引发问题。有关如何使用 `SetByCallers` 的更多信息，请参阅 [`SetByCallers`](#concepts-ge-spec-setbycaller)。 |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 乘法和除法修饰符
默认情况下，所有 `Multiply` 和 `Divide` `修饰符` 在乘入或除入 `Attribute` 的 `BaseValue` 之前会先相加。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*来自 `GameplayEffectAggregator.cpp`*

在公式中，`Multiply` 和 `Divide` 修饰符的 `Bias` 值为 `1`（`Addition` 的 `Bias` 为 `0`）。因此，公式看起来像这样：

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

这种公式导致了一些意想不到的结果。首先，这个公式会将所有修饰符相加，然后再乘或除以 `BaseValue`。多数人会期望直接将它们相乘或相除。例如，如果有两个 `Multiply` 修饰符值为 `1.5`，多数人会期望 `BaseValue` 乘以 `1.5 x 1.5 = 2.25`。但实际上，该公式将两个 `1.5` 相加，使 `BaseValue` 乘以 `2`（“50% 增加 + 再增加 50% = 100% 增加”）。例如，在 `GameplayPrediction.h` 的示例中，一个 `10%` 的速度增益对 `500` 的基础速度将变为 `550`。再增加一个 `10%` 的速度增益，将变为 `600`。

其次，该公式包含了一些未记录的关于可用数值的规则，这些规则是为 Paragon 游戏设计的。

乘法`Multiply`和除法`Divide`修饰符的相加公式规则如下：
* `(不能有多个值 < 1) 且 (任意数量的值 [1, 2))`
* `或 (一个值 >= 2)`

公式中的 `Bias` 基本上会减去范围 `[1, 2)` 内数值的整数部分。第一个修饰符的 `Bias` 会从初始 `Sum` 值（在循环开始前被设置为 `Bias`）中减去，这就是为什么单个值能正常工作，以及为什么一个 `< 1` 的值可以与 `[1, 2)` 范围内的数值一起工作。

一些乘法`Multiply`修饰符的示例：  
修饰符：`0.5`  
```
1 + (0.5 - 1) = 0.5，正确
```

修饰符：`0.5, 0.5`  
```
1 + (0.5 - 1) + (0.5 - 1) = 0，错误，预期结果是 1。
```
多个小于 `1` 的值在相加公式中没有意义。Paragon 的设计是只使用 [最大的负值修饰符](#cae-nonstackingge)，所以对于 `Multiply` 修饰符来说，只会有一个小于 `1` 的值参与计算。

修饰符：`1.1, 0.5`  
```
1 + (0.5 - 1) + (1.1 - 1) = 0.6，正确
```

修饰符：`5, 5`  
```
1 + (5 - 1) + (5 - 1) = 9，错误，预期结果是 10。
```
这种情况结果总是等于 `修饰符之和 - 修饰符数量 + 1`。

许多游戏可能希望乘法`Multiply` 和除法`Divide` 修饰符在应用于 `BaseValue` 之前相乘或相除。为实现这一点，需要**更改引擎代码**中的 `FAggregatorModChannel::EvaluateWithBase()`：

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	float Division = MultiplyMods(Mods[EGameplayModOp::Division], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

修改 `MultiplyMods` 的实现如下：

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```
**[⬆ 返回顶部](#table-of-contents)**

### 4.5.4.2 修饰符上的 Gameplay Tags

`SourceTags` 和 `TargetTags` 可以为每个 [Modifier](#concepts-ge-mods) 设置，作用类似于 `GameplayEffect` 的 [`Application Tag requirements`](#concepts-ge-tags)。这些标签只在效果应用时被考虑。也就是说，对于周期性或无限效果，它们只在效果首次应用时生效，而不会在每次周期性执行时生效。

#### 基于属性的修饰符的过滤器

基于属性的修饰符还可以设置 `SourceTagFilter` 和 `TargetTagFilter`。在确定作为修饰符源的属性的数值时，这些过滤器会排除某些与该属性相关的修饰符。如果修饰符的来源或目标没有符合过滤器的标签，则该修饰符会被排除。

**[⬆ 返回顶部](#table-of-contents)**

### 4.5.5 堆叠的 Gameplay Effects

`GameplayEffects` 默认会在应用时添加新的 `GameplayEffectSpec` 实例，而不会关心之前是否存在实例。通过设置堆叠规则，可以让新的 `GameplayEffectSpec` 不会创建新实例，而是改变当前实例的堆叠计数。堆叠仅适用于 `Duration` 和 `Infinite` 类型的 `GameplayEffects`。

两种堆叠类型：按来源聚合（Aggregate by Source）和按目标聚合(Aggregate by Target)。

| 堆叠类型             | 描述                                                                 |
| -------------------- | ------------------------------------------------------------------- |
| 按来源聚合 (Aggregate by Source) | 每个来源 `ASC` 在目标上有一个独立的堆叠实例，每个来源可以应用一定数量的堆叠。 |
| 按目标聚合 (Aggregate by Target) | 目标上只有一个共享堆叠实例，无论来源，每个来源可以应用一个堆叠，直至达到上限。 |

堆叠还有到期、持续时间刷新和周期重置的策略，这些可以在 `GameplayEffect` 蓝图中通过悬停工具提示查看。


示例项目包括一个自定义的蓝图节点，用于监听 `GameplayEffect` 堆叠变化。HUD 的 UMG 小部件使用它来更新玩家的被动护甲堆叠数量。这个 `AsyncTask` 将持续运行，直到手动调用 `EndTask()`，如在小部件的 `Destruct` 事件中调用。查看 `AsyncTaskEffectStackChanged.h/cpp`。

![监听 GameplayEffect 堆叠变化的蓝图节点](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-ge-ga"></a> 
#### 4.5.6 授予能力
`GameplayEffects` 可以向 `ASCs` 授予新的 [`GameplayAbilities`](#concepts-ga)。只有 `Duration` 和 `Infinite` 类型的 `GameplayEffects` 可以授予能力。

一个常见的用例是，当你想强制其他玩家执行某些操作时，例如将他们从击退或拉扯中移动出来。你可以对他们应用一个 `GameplayEffect`，该效果授予他们一个自动激活的能力（关于如何在授予能力时自动激活，请参见 [Passive Abilities](#concepts-ga-activating-passive)），以便对他们执行所需的操作。

设计人员可以选择 `GameplayEffect` 授予哪些能力，授予的等级，绑定的 [输入](#concepts-ga-input) 以及授予能力的移除策略。

| 移除策略               | 描述                                                                                                                                                                     |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 立即取消能力            | 当授予该能力的 `GameplayEffect` 从目标移除时，授予的能力立即取消并移除。                                                                                                   |
| 在结束时移除            | 允许授予的能力完成后再从目标移除。                                                                                                                                         |
| 不执行任何操作          | 授予的能力不受授予的 `GameplayEffect` 从目标移除的影响。目标永久拥有该能力，直到手动移除。                                                                                   |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 Gameplay Effect 标签
`GameplayEffects` 包含多个 [`GameplayTagContainers`](#concepts-gt)。设计人员将编辑每个类别的 `Added` 和 `Removed` `GameplayTagContainers`，并在编译时将结果显示在 `Combined` `GameplayTagContainer` 中。`Added` 标签是此 `GameplayEffect` 添加的新的标签，而父级之前没有。`Removed` 标签是父类拥有但此子类没有的标签。

| 类别                              | 描述                                                                                                                                                                                                                      |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect 资源标签          | `GameplayEffect` 拥有的标签。它们本身不执行任何功能，仅用于描述 `GameplayEffect`。                                                                                                 |
| 授予的标签                        | 存在于 `GameplayEffect` 上的标签，同时授予应用了该 `GameplayEffect` 的 `ASC`。当 `GameplayEffect` 被移除时，这些标签也会从 `ASC` 中移除。此功能仅适用于 `Duration` 和 `Infinite` 类型的 `GameplayEffects`。   |
| 持续标签需求                      | 一旦应用，这些标签决定 `GameplayEffect` 是启用还是禁用。即使 `GameplayEffect` 因未满足需求而禁用，它仍然被应用。如果后来满足了需求，`GameplayEffect` 将重新启用并重新应用其修饰符。仅适用于 `Duration` 和 `Infinite` 类型。 |
| 应用标签需求                      | 目标上的标签决定是否可以将 `GameplayEffect` 应用到目标。如果未满足这些需求，则 `GameplayEffect` 不会被应用。                                                                      |
| 通过标签移除 Gameplay Effects     | 当此 `GameplayEffect` 成功应用时，目标上拥有其 `Asset Tags` 或 `Granted Tags` 的 `GameplayEffects` 将被移除。                                                                    |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 免疫（Immunity）
`GameplayEffects` 可以通过 [`GameplayTags`](#concepts-gt) 提供免疫，阻止其他 `GameplayEffects` 的应用。尽管可以通过 `Application Tag Requirements` 等其他方式实现免疫，但使用该系统提供了一个用于处理因免疫而阻止的 `GameplayEffects` 的委托 `UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`。

`GrantedApplicationImmunityTags` 检查源 `ASC` 是否（包括源能力的 `AbilityTags`）拥有指定的标签。这是根据标签为某些角色或来源提供免疫的一种方式。

`Granted Application Immunity Query` 检查传入的 `GameplayEffectSpec` 是否符合阻止或允许其应用的查询条件。

查询工具在 `GameplayEffect` 蓝图中提供有用的悬停工具提示。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 游戏效果规范（Gameplay Effect Spec）
[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`) 可视为 `GameplayEffects` 的实例。它包含其表示的 `GameplayEffect` 类的引用、创建时的等级以及创建者。与 `GameplayEffects` 不同，它们应在运行时之前由设计人员创建，`GameplayEffectSpec` 可以在运行时自由创建和修改。应用 `GameplayEffect` 时，会从 `GameplayEffect` 创建一个 `GameplayEffectSpec`，并将其应用于目标。

`GameplayEffectSpecs` 通过 `UAbilitySystemComponent::MakeOutgoingSpec()`（`BlueprintCallable`）从 `GameplayEffects` 创建。`GameplayEffectSpecs` 不需要立即应用。常见用法是将一个 `GameplayEffectSpec` 传递给由能力创建的投射物，该投射物可以稍后应用于其击中的目标。当 `GameplayEffectSpecs` 成功应用时，它们返回一个新的结构 `FActiveGameplayEffect`。

`GameplayEffectSpec` 的主要内容包括：
* 创建该 `GameplayEffectSpec` 的 `GameplayEffect` 类。
* `GameplayEffectSpec` 的等级。通常与创建它的能力的等级相同，但可以不同。
* `GameplayEffectSpec` 的持续时间。默认为 `GameplayEffect` 的持续时间，但可以不同。
* 周期性效果的周期。默认为 `GameplayEffect` 的周期，但可以不同。
* 当前的堆叠计数。堆叠限制由 `GameplayEffect` 决定。
* [`GameplayEffectContextHandle`](#concepts-ge-context)，指出谁创建了该 `GameplayEffectSpec`。
* 创建 `GameplayEffectSpec` 时由于快照捕获的 `Attributes`。
* 除了 `GameplayEffect` 授予的 `GameplayTags` 外，该 `GameplayEffectSpec` 授予目标的 `DynamicGrantedTags`。
* 除了 `GameplayEffect` 的 `AssetTags` 外，该 `GameplayEffectSpec` 拥有的 `DynamicAssetTags`。
* `SetByCaller` 的 `TMaps`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 调用者设置（SetByCallers）
`SetByCallers` 允许 `GameplayEffectSpec` 使用与 `GameplayTag` 或 `FName` 关联的浮点值。这些值存储在其对应的 `TMaps` 中：`TMap<FGameplayTag, float>` 和 `TMap<FName, float>`。这些可以作为 `GameplayEffect` 的 `Modifiers` 或作为传递浮点数的通用方式使用。常见用法是将能力中生成的数值数据通过 `SetByCallers` 传递给 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 或 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)。

| `SetByCaller` 用途 | 说明                                                                                                                                                                                                                                                                                                              |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | 必须提前在 `GameplayEffect` 类中定义。只能使用 `GameplayTag` 版本。如果在 `GameplayEffect` 类中定义，但 `GameplayEffectSpec` 没有相应的标签和浮点值对，则应用 `GameplayEffectSpec` 时会在运行时出错并返回 0。这可能会导致 `Divide` 操作的问题。参见 [`Modifiers`](#concepts-ge-mods)。 |
| 其他用途           | 无需提前在任何地方定义。在 `GameplayEffectSpec` 上读取不存在的 `SetByCaller` 值时，可以返回开发人员定义的默认值并显示可选警告。                                                                                                                                                                       |

要在蓝图中分配 `SetByCaller` 值，使用所需版本的蓝图节点（`GameplayTag` 或 `FName`）：

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

要在蓝图中读取 `SetByCaller` 值，需要在蓝图库中创建自定义节点。

要在 C++ 中分配 `SetByCaller` 值，使用所需版本的函数（`GameplayTag` 或 `FName`）：

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

要在 C++ 中读取 `SetByCaller` 值，使用所需版本的函数（`GameplayTag` 或 `FName`）：

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
我建议使用```GameplayTag```版本而不是```FName```版本。这可以防止蓝图中出现拼写错误。

**[⬆ 返回顶部](#table-of-contents)**



<a name="concepts-ge-context"></a>
#### 4.5.10 游戏效果上下文 (Gameplay Effect Context)
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) 结构体包含了 `GameplayEffectSpec` 的发起者以及 [`TargetData`](#concepts-targeting-data) 的信息。这也是一个很好的结构体，可以通过子类化来在 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)、[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)、[`AttributeSets`](#concepts-as) 和 [`GameplayCues`](#concepts-gc) 等模块之间传递任意数据。

要子类化 `GameplayEffectContext`：

1. 子类化 `FGameplayEffectContext`。
2. 重写 `FGameplayEffectContext::GetScriptStruct()`。
3. 重写 `FGameplayEffectContext::Duplicate()`。
4. 如果新数据需要被复制，重写 `FGameplayEffectContext::NetSerialize()`。
5. 为子类实现 `TStructOpsTypeTraits`，如父类结构 `FGameplayEffectContext`。
6. 在 [`AbilitySystemGlobals`](#concepts-asg) 类中重写 `AllocGameplayEffectContext()`，以返回子类的一个新对象。

[GASShooter](https://github.com/tranek/GASShooter) 使用了一个子类化的 `GameplayEffectContext` 来添加 `TargetData`，使其可以在 `GameplayCues` 中访问，特别是用于霰弹枪，因为它可以击中多个敌人。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 修饰符强度计算 (Modifier Magnitude Calculation)
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html) (`ModMagCalc` 或 `MMC`) 是用于 `GameplayEffects` 中 [`Modifiers`](#concepts-ge-mods) 的强大类。这些功能类似于 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)，但功能稍弱，并且最重要的是它们是 [可预测的](#concepts-p)。它们的唯一目的是从 `CalculateBaseMagnitude_Implementation()` 返回一个浮点值。可以通过蓝图和 C++ 子类化并重写此函数。

`MMCs` 可用于任何类型的 `GameplayEffects` 持续时间 - `Instant`（瞬时）、`Duration`（持续）、`Infinite`（无限）或 `Periodic`（周期）。

`MMCs` 的优势在于能够捕获 `GameplayEffect` 的 `Source` 或 `Target` 上任意数量的 `Attributes` 的值，并完全访问 `GameplayEffectSpec` 来读取 `GameplayTags` 和 `SetByCallers`。`Attributes` 可以是快照的或非快照的。快照的 `Attributes` 在创建 `GameplayEffectSpec` 时捕获，而非快照的 `Attributes` 在应用 `GameplayEffectSpec` 时捕获，并在 `Infinite` 和 `Duration` 类型的 `GameplayEffects` 中，随着 `Attribute` 的变化自动更新。捕获 `Attributes` 会从 `ASC` 中现有的修改器重新计算它们的 `CurrentValue`。此重新计算不会运行 `AbilitySet` 中的 [`PreAttributeChange()`](#concepts-as-preattributechange)，因此需要在这里再次进行任何必要的限制。

| 快照   | Source 或 Target | 在 `GameplayEffectSpec` 上捕获   | 随着 `Attribute` 的变化自动更新（针对 `Infinite` 或 `Duration` 类型的 `GE`） |
|--------|------------------|----------------------------------|--------------------------------------------------------------------------------|
| 是     | Source           | 创建时                           | 否                                                                             |
| 是     | Target           | 应用时                           | 否                                                                             |
| 否     | Source           | 应用时                           | 是                                                                             |
| 否     | Target           | 应用时                           | 是                                                                             |

来自 `MMC` 的浮点结果可以在 `GameplayEffect` 的 `Modifier` 中进一步被系数及系数前后的加成所修改。

以下是一个示例 `MMC`，它捕获 `Target` 的法力值 (`Mana`) 属性，并基于 `Mana` 的多少和是否拥有特定的标签来减少其值（例如毒性效果）：
```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{
	// ManaDef 在头文件中定义为 FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	// MaxManaDef 在头文件中定义为 FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// 获取源和目标的标签，这可能会影响使用哪些增益效果
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // 避免除以零

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// 如果目标的法力值大于一半，效果加倍
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// 如果目标对毒性法力弱点敏感，效果加倍
		Reduction *= 2;
	}
	
	return Reduction;
}
```

如果你没有在 `MMC` 的构造函数中将 `FGameplayEffectAttributeCaptureDefinition` 添加到 `RelevantAttributesToCapture` 中，并尝试捕获 `Attributes`，则会遇到有关捕获时缺少 `Spec` 的错误。如果你不需要捕获 `Attributes`，则不必向 `RelevantAttributesToCapture` 添加任何内容。

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-ge-ec"></a>
#### 4.5.12 游戏玩法效果执行计算
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html)（`ExecutionCalculation`、`Execution`（你会经常在插件的源代码中看到这个术语）或 `ExecCalc`）是 `GameplayEffects` 更改 `ASC`（角色状态组件）最强大的方式。与 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) 类似，它们可以捕捉 `Attributes`（属性），并可以选择性地快照这些属性。不同于 `MMCs`，它们不仅可以更改一个 `Attribute`，还可以做程序员想要的任何其他操作。这种强大和灵活性的缺点是它们不能被[预测](#concepts-p)，并且必须用 C++ 实现。

`ExecutionCalculations` 只能与 `Instant` 和 `Periodic` 类型的 `GameplayEffects` 一起使用。任何包含“Execute”一词的内容通常指的就是这两种类型的 `GameplayEffects`。

快照捕捉会在 `GameplayEffectSpec` 创建时捕捉 `Attribute`，而不快照则会在 `GameplayEffectSpec` 应用时捕捉 `Attribute`。捕捉 `Attributes` 会重新计算它们的 `CurrentValue`（当前值），这个值来自 `ASC` 上现有的修改器。此重新计算**不会**在 `AbilitySet` 中运行 [`PreAttributeChange()`](#concepts-as-preattributechange)，因此任何限制操作必须在这里再次执行。

| 快照 | 来源或目标 | 在 `GameplayEffectSpec` 上捕捉 |
| ---- | ---------- | ---------------------------- |
| 是   | 来源       | 创建时                       |
| 是   | 目标       | 应用时                       |
| 否   | 来源       | 应用时                       |
| 否   | 目标       | 应用时                       |

为了设置 `Attribute` 捕捉，我们遵循 Epic 的 ActionRPG 示例项目所设定的模式，定义一个结构体来持有并定义如何捕捉 `Attributes`，并在结构体的构造函数中创建一个副本。每个 `ExecCalc` 都会有一个类似的结构体。**注意：** 每个结构体需要一个唯一的名称，因为它们共享同一个命名空间。使用相同的结构体名称会导致捕捉 `Attributes` 时行为不正确（主要是捕捉了错误的 `Attributes` 值）。

对于 `Local Predicted`（本地预测）、`Server Only`（仅限服务器）和 `Server Initiated`（服务器发起）的 [`GameplayAbilities`](#concepts-ga)，`ExecCalc` 只会在服务器上调用。

基于从 `Source`（源）和 `Target`（目标）上的多个属性读取的复杂公式来计算所受伤害，是 `ExecCalc` 最常见的一个示例。包含的示例项目有一个简单的 `ExecCalc`，用来计算伤害，它读取 `GameplayEffectSpec` 的 [`SetByCaller`](#concepts-ge-spec-setbycaller) 中的伤害值，并根据从 `Target` 捕捉到的护甲 `Attribute` 来缓解这个值。请查看 `GDDamageExecCalculation.cpp/.h`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 发送数据到执行计算
除了捕捉 `Attributes` 外，还有几种方式可以向 `ExecutionCalculation` 发送数据。

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
任何在 `GameplayEffectSpec` 上设置的 [`SetByCallers`](#concepts-ge-spec-setbycaller) 都可以直接在 `ExecutionCalculation` 中读取。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 后台数据属性计算修改器
如果你想将值硬编码到 `GameplayEffect` 中，可以使用 `CalculationModifier`，它使用捕捉到的一个 `Attribute` 作为后台数据来传递这些值。

在这个截图示例中，我们正在将 50 加到捕捉到的伤害 `Attribute` 上。你也可以将其设置为 `Override`，只使用硬编码的值。

![后台数据属性计算修改器](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

`ExecutionCalculation` 在捕捉到 `Attribute` 时读取这个值。

```c++
float Damage = 0.0f;
// 捕捉在伤害 GE 中作为 CalculationModifier 设置的可选伤害值
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 后台数据临时变量计算修改器
如果你想将值硬编码到 `GameplayEffect` 中，可以使用 `CalculationModifier`，它使用 `Temporary Variable`（在 C++ 中称为 `Transient Aggregator`）。`Temporary Variable` 与 `GameplayTag` 相关联。

在这个截图示例中，我们正在使用 `Data.Damage` `GameplayTag` 将 50 加到 `Temporary Variable` 上。

![后台数据临时变量计算修改器](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

将后台 `Temporary Variables` 添加到 `ExecutionCalculation` 的构造函数中：

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation` 使用与 `Attribute` 捕捉函数类似的特殊捕捉函数来读取这个值。

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 游戏玩法效果上下文
你可以通过自定义的 [`GameplayEffectContext`](#concepts-ge-context) 向 `ExecutionCalculation` 发送数据。

在 `ExecutionCalculation` 中，你可以从 `FGameplayEffectCustomExecutionParameters` 获取 `EffectContext`。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

如果你需要修改 `GameplayEffectSpec` 或 `EffectContext`：

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

在 `ExecutionCalculation` 中修改 `GameplayEffectSpec` 时需要小心。请查看 `GetOwningSpecForPreExecuteMod()` 的注释。

```c++
/** 非常量访问。修改规格时要小心，特别是在属性捕捉之后。 */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-ge-car"></a>
#### 4.5.13 自定义应用需求
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html)（`CAR`）类使设计师可以更高级地控制是否可以应用 `GameplayEffect`，而不仅仅是对 `GameplayEffect` 的简单 `GameplayTag` 检查。可以通过在 Blueprint 中重写 `CanApplyGameplayEffect()`，或者在 C++ 中重写 `CanApplyGameplayEffect_Implementation()` 来实现。

使用 `CAR` 的示例：
* `Target` 需要有一定数量的 `Attribute`
* `Target` 需要拥有一定数量的 `GameplayEffect` 堆叠

`CAR` 还可以做更高级的操作，例如检查目标上是否已经存在此 `GameplayEffect` 的实例，并[改变持续时间](#concepts-ge-duration)，而不是应用新的实例（在 `CanApplyGameplayEffect()` 中返回 `false`）。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 消耗型 GameplayEffect
[`GameplayAbilities`](#concepts-ga) 有一个可选的 `GameplayEffect`，专门用于作为能力的消耗。消耗指的是 `ASC` 为了激活 `GameplayAbility` 需要消耗多少 `Attribute`。如果 `GA` 无法承受 `Cost GE`，则无法激活该能力。此 `Cost GE` 应该是一个 `Instant` 类型的 `GameplayEffect`，并包含一个或多个从 `Attributes` 中减去的 `Modifiers`。默认情况下，`Cost GE` 应该是可预测的，建议保持该能力，即不要使用 `ExecutionCalculations`。对于复杂的消耗计算，`MMC` 是完全可接受并鼓励使用的。

在开始时，您最有可能为每个有消耗的 `GA` 创建一个独特的 `Cost GE`。一种更高级的技术是重用一个 `Cost GE`，用于多个 `GA`，并通过修改从 `Cost GE` 创建的 `GameplayEffectSpec` 来加入 `GA` 特定的数据（消耗值由 `GA` 定义）。**这仅适用于 `Instanced` 类型的能力。**

重用 `Cost GE` 的两种技术：

1. **使用 `MMC`。** 这是最简单的方法。创建一个 [`MMC`](#concepts-ge-mmc)，从 `GameplayAbility` 实例中读取消耗值，您可以通过 `GameplayEffectSpec` 获取此值。

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

在此示例中，消耗值是 `GameplayAbility` 子类中的一个 `FScalableFloat`，我已将其添加到类中。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **重写 `UGameplayAbility::GetCostGameplayEffect()`。** 重写此函数，并在运行时[创建一个 `GameplayEffect`](#concepts-ge-dynamic)，读取 `GameplayAbility` 上的消耗值。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 冷却型 GameplayEffect
[`GameplayAbilities`](#concepts-ga) 有一个可选的 `GameplayEffect`，专门用于作为能力的冷却。冷却决定了能力激活后多久才能再次激活。如果 `GA` 仍处于冷却中，则无法激活该能力。此 `Cooldown GE` 应该是一个 `Duration` 类型的 `GameplayEffect`，没有 `Modifiers`，并且每个 `GameplayAbility` 或每个能力槽（如果游戏中有可互换的能力分配到槽位并共享冷却）应有一个独特的 `GameplayTag`，位于 `GameplayEffect` 的 `GrantedTags` 中（"Cooldown Tag"）。`GA` 实际上是检查 `Cooldown Tag` 的存在，而不是 `Cooldown GE` 的存在。默认情况下，`Cooldown GE` 应该是可预测的，建议保持该能力，即不要使用 `ExecutionCalculations`。对于复杂的冷却计算，`MMC` 是完全可接受并鼓励使用的。

在开始时，您最有可能为每个有冷却的 `GA` 创建一个独特的 `Cooldown GE`。一种更高级的技术是重用一个 `Cooldown GE`，用于多个 `GA`，并通过修改从 `Cooldown GE` 创建的 `GameplayEffectSpec` 来加入 `GA` 特定的数据（冷却持续时间和 `Cooldown Tag` 由 `GA` 定义）。**这仅适用于 `Instanced` 类型的能力。**

重用 `Cooldown GE` 的两种技术：

1. **使用 [`SetByCaller`](#concepts-ge-spec-setbycaller)。** 这是最简单的方法。将共享的 `Cooldown GE` 的持续时间设置为 `SetByCaller`，并带有一个 `GameplayTag`。在 `GameplayAbility` 子类中，定义一个浮动值 / `FScalableFloat` 用于持续时间，一个 `FGameplayTagContainer` 用于唯一的 `Cooldown Tag`，以及一个临时的 `FGameplayTagContainer`，我们将在 `GetCooldownTags()` 中使用它作为返回指针，返回我们 `Cooldown Tag` 和 `Cooldown GE` 的标签的联合。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// 临时容器，我们将在 GetCooldownTags() 中返回它的指针。
// 这将是我们 CooldownTags 和 Cooldown GE 的冷却标签的联合。
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

然后重写 `UGameplayAbility::GetCooldownTags()`，以返回 `Cooldown Tags` 和任何现有的 `Cooldown GE` 标签的联合。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags 写入 TempCooldownTags 的 CDO，因此在能力冷却标签发生变化时（例如移到不同的槽位）清空它
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重写 `UGameplayAbility::ApplyCooldown()`，将我们的 `Cooldown Tags` 注入到冷却 `GameplayEffectSpec` 中。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

在这张图中，冷却的持续时间 `Modifier` 被设置为 `SetByCaller`，并带有一个 `Data Tag`，名为 `Data.Cooldown`。`Data.Cooldown` 在上面的代码中会是 `OurSetByCallerTag`。

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **使用 [`MMC`](#concepts-ge-mmc)。** 这与上面的设置相同，唯一不同的是在 `Cooldown GE` 和 `ApplyCooldown` 中设置 `SetByCaller` 作为持续时间。相反，将持续时间设置为一个 `Custom Calculation Class`，并指向我们将创建的新 `MMC`。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// 临时容器，我们将在 GetCooldownTags() 中返回它的指针。
// 这将是我们 CooldownTags 和 Cooldown GE 的冷却标签的联合。
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```


然后重写 `UGameplayAbility::GetCooldownTags()`，以返回我们 `Cooldown Tags` 和任何现有的 `Cooldown GE` 标签的联合。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags 写入 TempCooldownTags 的 CDO，因此在能力冷却标签发生变化时（例如移到不同的槽位）清空它
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重写 `UGameplayAbility::ApplyCooldown()`，将我们的 `Cooldown Tags` 注入到冷却 `GameplayEffectSpec` 中。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 获取冷却型 GameplayEffect 的剩余时间
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**注意：** 在客户端查询冷却剩余时间需要它们能够接收复制的 `GameplayEffects`。这将取决于它们的 `ASC` 的[复制模式](#concepts-asc-rm)。

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 监听冷却开始和结束
要监听冷却何时开始，您可以响应 `Cooldown GE` 被应用时，通过绑定到 `AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf`，或者当 `Cooldown Tag` 被添加时，通过绑定到 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`。我建议监听 `Cooldown GE` 被添加的情况，因为您还可以访问应用该 `Cooldown GE` 的 `GameplayEffectSpec`。通过它，您可以确定 `Cooldown GE` 是本地预测的，还是服务器的修正版本。

要监听冷却何时结束，您可以响应 `Cooldown GE` 被移除时，通过绑定到 `AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`，或者当 `Cooldown Tag` 被移除时，通过绑定到 `AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`。我建议监听 `Cooldown Tag` 被移除的情况，因为当服务器修正的 `Cooldown GE` 到来时，它将移除我们本地预测的 `Cooldown GE`，即使我们仍然处于冷却状态，`OnAnyGameplayEffectRemovedDelegate()` 也会触发。`Cooldown Tag` 在预测的 `Cooldown GE` 移除和服务器修正的 `Cooldown GE` 应用期间不会变化。

**注意：** 在客户端监听 `GameplayEffect` 的添加或移除，要求它们能够接收复制的 `GameplayEffects`。这将取决于它们的 `ASC` 的[复制模式](#concepts-asc-rm)。

示例项目包括一个自定义的 Blueprint 节点，用于监听冷却开始和结束。HUD UMG Widget 使用它来更新陨石冷却的剩余时间。这个 `AsyncTask` 将一直存在，直到手动调用 `EndTask()`，我们在 UMG Widget 的 `Destruct` 事件中调用它。见 [`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp)。

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)



<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 预测冷却时间
目前冷却时间无法真正预测。当本地预测的 `Cooldown GE` 被应用时，我们可以开始 UI 冷却计时器，但 `GameplayAbility` 的实际冷却时间是与服务器上的冷却时间剩余相对应的。根据玩家的延迟，本地预测的冷却时间可能已经过期，但服务器上的 `GameplayAbility` 仍然处于冷却状态，这将阻止 `GameplayAbility` 的立即重新激活，直到服务器上的冷却时间结束。

示例项目通过在本地预测的冷却时间开始时将陨石技能的 UI 图标灰化处理，然后在服务器的修正后的 `Cooldown GE` 数据到来时开始冷却计时器。

这一游戏机制的后果是，高延迟的玩家在短冷却技能上的射击频率低于低延迟的玩家，从而处于劣势。Fortnite 通过其武器拥有自定义的账本记录，避免使用冷却 `GameplayEffects`。

允许真正预测的冷却时间（玩家可以在本地冷却结束时激活 `GameplayAbility`，但服务器仍然处于冷却状态）是 Epic 希望在 [未来版本的 GAS](#concepts-p-future) 中实现的功能。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 更改激活 Gameplay Effect 的持续时间
要更改 `Cooldown GE` 或任何 `Duration` 类型的 `GameplayEffect` 的剩余时间，我们需要更改 `GameplayEffectSpec` 的 `Duration`，更新其 `StartServerWorldTime`，更新其 `CachedStartServerWorldTime`，更新其 `StartWorldTime`，并重新运行 `CheckDuration()` 来检查持续时间。这样做会在服务器上进行，并通过标记 `FActiveGameplayEffect` 为脏数据，确保更改会同步到客户端。
**注意：** 这涉及到 `const_cast`，并可能不是 Epic 意图的更改持续时间的方式，但目前看起来效果不错。

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 在运行时创建动态 Gameplay Effects
在运行时创建动态 `GameplayEffects` 是一个高级主题，你不应当频繁这么做。

只有 `Instant` 类型的 `GameplayEffects` 可以在 C++ 中从头开始在运行时创建。`Duration` 和 `Infinite` 类型的 `GameplayEffects` 不能在运行时动态创建，因为当它们进行复制时，系统会查找 `GameplayEffect` 类定义，而该定义并不存在。为了实现这一功能，你应该像在编辑器中那样创建一个原型 `GameplayEffect` 类，然后在运行时根据需要自定义 `GameplayEffectSpec` 实例。

在运行时创建的 `Instant` 类型的 `GameplayEffects` 也可以在 [本地预测](#concepts-p) 的 `GameplayAbility` 中调用。然而，目前尚不清楚动态创建是否会有副作用。

##### 示例

示例项目创建了一个动态的即时 `GameplayEffect`，当角色造成致命一击时，将金币和经验点数返还给击杀者。

```c++
// 创建一个动态的即时 Gameplay Effect 以给予奖励
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

第二个示例展示了在本地预测的 `GameplayAbility` 中创建的运行时 `GameplayEffect`。使用时请小心（参见代码中的注释）！

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// 在运行时创建 GE
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // 只有即时 GE 支持运行时创建

		// 添加一个简单的可缩放浮动修改器，将 MyAttribute 修改为 42。
		// 在实际应用中，可使用 TriggerEventData 中传递的信息。
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// 应用 GE

		// 在这里创建 GESpec 以避免 ASC 创建基于 GE 类默认对象的 GESpec。
		// 由于这里是动态 GE，这将创建一个带有基本 GameplayEffect 类的 GESpec，因此我们
		// 将丢失修改器。请注意：目前尚不清楚这种 "hack" 是否会产生副作用！
		// Spec 防止 GE 对象被垃圾回收，因为 GE 是 GESpec 上的 UPROPERTY。
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new"，因为生命周期由 handle 内的 shared_ptr 管理
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-ge-containers"></a>
#### 4.5.18 游戏效果容器(Gameplay Effect Containers)
Epic的[动作RPG示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)实现了一种结构，名为`FGameplayEffectContainer`。这些在原版GAS中并不存在，但它们对于包含`GameplayEffects`和[`TargetData`](#concepts-targeting-data)非常有用。它可以自动化一些操作，比如从`GameplayEffects`创建`GameplayEffectSpecs`并在其`GameplayEffectContext`中设置默认值。在`GameplayAbility`中创建`GameplayEffectContainer`并将其传递给生成的投射物非常简单直观。我选择不在包含的示例项目中实现`GameplayEffectContainers`，以展示如何在没有它们的情况下使用原版GAS，但我强烈建议你了解它们，并考虑将其添加到你的项目中。

要访问`GameplayEffectContainers`中的`GESpecs`，以执行如添加`SetByCallers`等操作，可以通过索引访问`GESpec`引用，在`GESpecs`数组中查找。你需要提前知道想要访问的`GESpec`的索引。

![SetByCaller与GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers`还包含一个可选的高效[目标](#concepts-targeting-containers)功能。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 4.6.1 Gameplay Ability定义
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html)（`GA`）是指`Actor`在游戏中可以执行的任何动作或技能。例如，冲刺和开枪可以同时进行。可以在Blueprint或C++中创建这些技能。

`GameplayAbilities`的示例：
* 跳跃
* 冲刺
* 开枪
* 每隔X秒被动格挡一次攻击
* 使用药水
* 开门
* 收集资源
* 建造建筑

不应使用`GameplayAbilities`实现的功能：
* 基本移动输入
* 与UI的一些交互 - 不要使用`GameplayAbility`来从商店购买物品。

这些不是规则，仅是我的建议。你的设计和实现可能会有所不同。

`GameplayAbilities`具有默认功能，用于修改属性的变化量，或改变`GameplayAbility`的功能。

`GameplayAbilities`的执行取决于[`Net Execution Policy`](#concepts-ga-net)，即它们是否在拥有的客户端和/或服务器上运行，但不会在模拟代理上运行。`Net Execution Policy`决定了`GameplayAbility`是否会被本地[预测](#concepts-p)。它们包含[可选的成本和冷却`GameplayEffects`](#concepts-ga-commit)的默认行为。`GameplayAbilities`使用[`AbilityTasks`](#concepts-at)来处理需要时间的动作，比如等待事件、等待属性变化、等待玩家选择目标，或使用`Root Motion Source`移动`Character`。**模拟客户端不会运行`GameplayAbilities`**。相反，当服务器运行能力时，任何需要在模拟代理上播放的视觉效果（如动画蒙太奇）将通过`AbilityTasks`或[`GameplayCues`](#concepts-gc)进行复制或RPC，以处理如声音和粒子等装饰性内容。

所有`GameplayAbilities`都会重写其`ActivateAbility()`函数来执行你的游戏逻辑。你还可以在`EndAbility()`中添加额外的逻辑，当`GameplayAbility`完成或被取消时执行。

简单的`GameplayAbility`流程图：
![简单的GameplayAbility流程图](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

更复杂的`GameplayAbility`流程图：
![复杂的GameplayAbility流程图](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

复杂的能力可以通过多个`GameplayAbilities`来实现，这些能力彼此交互（激活、取消等）。

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 复制策略
不要使用这个选项。这个名称容易引起误解，你其实不需要它。默认情况下，`GameplayAbilitySpecs`会从服务器复制到拥有客户端。如前所述，**`GameplayAbilities`不会在模拟代理上运行**。它们使用`AbilityTasks`和`GameplayCues`来复制或RPC视觉变化到模拟代理。Epic的Dave Ratti表示他希望在未来[移除此选项](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)。

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 服务器尊重远程能力取消
这个选项通常会带来麻烦。它的意思是，如果客户端的`GameplayAbility`结束（无论是由于取消还是自然完成），它将强制服务器的版本结束，无论它是否完成。后者是一个重要问题，尤其是对于具有高延迟的玩家使用的本地预测`GameplayAbilities`。通常你会希望禁用这个选项。

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 直接复制输入
启用此选项将始终将输入按下和释放事件复制到服务器。Epic建议不要使用此选项，而是依赖于现有输入相关[`AbilityTasks`](#concepts-at)中内置的`Generic Replicated Events`，特别是当你已将[输入绑定到你的`ASC`](#concepts-ga-input)时。

Epic的评论：
```c++
// 直接输入状态复制。如果bReplicateInputDirectly在能力上为真，则会调用这些方法，通常不推荐使用此功能。（相反，建议使用通用复制事件）。
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 将输入绑定到ASC
`ASC`允许你直接将输入动作绑定到它，并在授予这些能力时将输入分配给`GameplayAbilities`。分配给`GameplayAbilities`的输入动作将在按下时自动激活这些能力，前提是满足`GameplayTag`要求。分配的输入动作要求使用内置的`AbilityTasks`，这些任务响应输入。

除了分配给`GameplayAbilities`激活的输入动作外，`ASC`还接受通用的`Confirm`和`Cancel`输入。这些特殊输入用于`AbilityTasks`，例如确认[`Target Actors`](#concepts-targeting-actors)或取消它们。

要将输入绑定到`ASC`，首先必须创建一个枚举，将输入动作名称转换为字节。枚举名称必须与项目设置中使用的输入动作名称完全匹配。`DisplayName`不重要。

来自示例项目：
```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 无
	None			UMETA(DisplayName = "None"),
	// 1 确认
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 取消
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 左键
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 右键
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 冲刺
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 跳跃
	Jump			UMETA(DisplayName = "Jump")
};
```

如果你的`ASC`位于`Character`上，那么在`SetupPlayerInputComponent()`中包括绑定到`ASC`的函数：
```c++
// 绑定到AbilitySystemComponent
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

如果你的`ASC`位于`PlayerState`上，那么在`SetupPlayerInputComponent()`中可能会存在竞争条件，因为`PlayerState`可能尚未复制到客户端。因此，我建议在`SetupPlayerInputComponent()`和`OnRep_PlayerState()`中尝试绑定输入。仅`OnRep_PlayerState()`本身是不够的，因为在`PlayerState`复制之前，`Actor`的`InputComponent`可能

为null，且在`PlayerController`告诉客户端调用`ClientRestart()`以创建`InputComponent`时，`PlayerState`已经复制。示例项目演示了在两个位置尝试绑定，并通过一个布尔值来控制，使得绑定操作只执行一次。

**注意：** 在示例项目中，枚举中的`Confirm`和`Cancel`与项目设置中的输入动作名称（`ConfirmTarget`和`CancelTarget`）不匹配，但我们在`BindAbilityActivationToInputComponent()`中提供了它们之间的映射。这些特殊输入不必匹配，但可以匹配。枚举中的所有其他输入必须与项目设置中的输入动作名称匹配。

对于仅通过一个输入激活的`GameplayAbilities`（它们总是处于同一“槽位”中，比如MOBA），我更喜欢在我的`UGameplayAbility`子类中添加一个变量，定义它们的输入。然后，我可以从`ClassDefaultObject`中读取此输入并授予该能力。

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 不自动激活能力的输入绑定
如果你不希望`GameplayAbilities`在按下输入时自动激活，但仍希望将它们绑定到输入，以便与`AbilityTasks`一起使用，你可以向`UGameplayAbility`子类添加一个新的布尔变量`bActivateOnInput`，默认值为`true`，并重写`UAbilitySystemComponent::AbilityLocalInputPressed()`。

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// 如果该InputID被GenericConfirm/Cancel重载且回调已绑定，则消耗输入
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// 调用InputPressed事件。这不会在这里复制。如果有人在监听，他们可能会将InputPressed事件复制到服务器。
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// 能力未激活，尝试激活它
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-ga-granting"></a>
#### 4.6.3 授予能力
将`GameplayAbility`授予`ASC`会将其添加到`ASC`的`ActivatableAbilities`列表中，使其在满足[`GameplayTag`](#concepts-ga-tags)要求时，可以随时激活该`GameplayAbility`。

我们在服务器上授予`GameplayAbilities`，然后自动将[`GameplayAbilitySpec`](#concepts-ga-spec)复制到拥有的客户端。其他客户端/模拟代理不会接收到`GameplayAbilitySpec`。

示例项目在`Character`类中存储了一个`TArray<TSubclassOf<UGDGameplayAbility>>`，并在游戏开始时读取并授予这些能力：
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// 仅在服务器上授予能力
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->bCharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->bCharacterAbilitiesGiven = true;
}
```

当授予这些`GameplayAbilities`时，我们会使用`UGameplayAbility`类、能力等级、绑定的输入和`SourceObject`（或授予该`GameplayAbility`的对象）来创建`GameplayAbilitySpecs`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 激活能力
如果一个`GameplayAbility`被分配了输入动作，那么当输入被按下并满足其`GameplayTag`要求时，它会自动激活。通常这并不是激活`GameplayAbility`的理想方式。`ASC`提供了四种其他激活`GameplayAbilities`的方法：通过`GameplayTag`、`GameplayAbility`类、`GameplayAbilitySpec`句柄和事件激活。通过事件激活`GameplayAbility`允许你[传递事件的数据有效载荷](#concepts-ga-data)。

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```
通过事件激活`GameplayAbility`时，`GameplayAbility`必须设置其`Triggers`。分配一个`GameplayTag`并选择一个`GameplayEvent`选项。要发送事件，使用函数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。通过事件激活`GameplayAbility`允许你传递数据有效载荷。

`GameplayAbility`的`Triggers`还允许你在添加或移除`GameplayTag`时激活`GameplayAbility`。

**注意：** 在Blueprint中从事件激活`GameplayAbility`时，必须使用`ActivateAbilityFromEvent`节点。

**注意：** 如果`GameplayAbility`应该终止，别忘了调用`EndAbility()`，除非你有一个始终运行的`GameplayAbility`，例如被动能力。

**本地预测**的`GameplayAbilities`激活顺序：
1. **拥有客户端**调用`TryActivateAbility()`
1. 调用`InternalTryActivateAbility()`
1. 调用`CanActivateAbility()`并返回是否满足`GameplayTag`要求，`ASC`是否能负担费用，`GameplayAbility`是否处于冷却状态，是否没有其他实例正在激活
1. 调用`CallServerTryActivateAbility()`并传递它生成的`Prediction Key`
1. 调用`CallActivateAbility()`
1. 调用`PreActivate()`，Epic称之为“初始化”
1. 最终调用`ActivateAbility()`激活能力

**服务器**接收到`CallServerTryActivateAbility()`
1. 调用`ServerTryActivateAbility()`
1. 调用`InternalServerTryActivateAbility()` 
1. 调用`InternalTryActivateAbility()`
1. 调用`CanActivateAbility()`并返回是否满足`GameplayTag`要求，`ASC`是否能负担费用，`GameplayAbility`是否处于冷却状态，是否没有其他实例正在激活
1. 如果成功，调用`ClientActivateAbilitySucceed()`，告诉客户端更新其`ActivationInfo`，并广播`OnConfirmDelegate`委托。这不同于输入确认。
1. 调用`CallActivateAbility()`
1. 调用`PreActivate()`，Epic称之为“初始化”
1. 最终调用`ActivateAbility()`激活能力

如果服务器在任何时候未能激活，它会调用`ClientActivateAbilityFailed()`，立即终止客户端的`GameplayAbility`并撤销任何预测的更改。

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 被动能力
要实现自动激活并持续运行的被动`GameplayAbilities`，需要重写`UGameplayAbility::OnAvatarSet()`，该函数会在`GameplayAbility`被授予并设置`AvatarActor`时自动调用，并调用`TryActivateAbility()`。

我建议在自定义的`UGameplayAbility`类中添加一个`bool`变量，指定`GameplayAbility`是否应在授予时激活。示例项目对其被动护甲堆叠能力做了这个处理。

被动`GameplayAbilities`通常会有一个`[Net Execution Policy](#concepts-ga-net)`，为`仅服务器`。

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (bActivateAbilityOnGranted)
	{
		ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic将这个函数描述为启动被动能力并进行`BeginPlay`类型操作的正确位置。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-activating-failedtags"></a>
##### 4.6.4.2 激活失败标签

能力有默认的逻辑来告诉你为什么激活失败。要启用此功能，你必须设置与默认失败情况对应的`GameplayTags`。

将这些标签（或你自己的命名约定）添加到你的项目中：
```
+GameplayTagList=(Tag="Activation.Fail.BlockedByTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.CantAffordCost",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.IsDead",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.MissingTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.Networking",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.OnCooldown",DevComment="")
```

然后将它们添加到[`GASDocumentation\Config\DefaultGame.ini`](https://github.com/tranek/GASDocumentation/blob/master/Config/DefaultGame.ini#L8-L13):
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
ActivateFailIsDeadName=Activation.Fail.IsDead
ActivateFailCooldownName=Activation.Fail.OnCooldown
ActivateFailCostName=Activation.Fail.CantAffordCost
ActivateFailTagsBlockedName=Activation.Fail.BlockedByTags
ActivateFailTagsMissingName=Activation.Fail.MissingTags
ActivateFailNetworkingName=Activation.Fail.Networking
```

现在，每当能力激活失败时，相应的`GameplayTag`将包含在输出日志消息中或显示在`showdebug AbilitySystem` HUD上。
```
LogAbilitySystem: Display: InternalServerTryActivateAbility. Rejecting ClientActivation of Default__GA_FireGun_C. InternalTryActivateAbility failed: Activation.Fail.BlockedByTags
LogAbilitySystem: Display: ClientActivateAbilityFailed_Implementation. PredictionKey :109 Ability: Default__GA_FireGun_C
```

![激活失败标签显示在showdebug AbilitySystem中](https://github.com/tranek/GASDocumentation/raw/master/Images/activationfailedtags.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 取消能力
要在内部取消`GameplayAbility`，调用`CancelAbility()`。这将调用`EndAbility()`并将其`WasCancelled`参数设置为`true`。

要在外部取消`GameplayAbility`，`ASC`提供了一些函数：

```c++
// 取消指定能力的CDO
void CancelAbility(UGameplayAbility* Ability);

// 取消由传入的spec句柄指定的能力。如果句柄未在重新激活的能力中找到，则不执行任何操作。
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

// 取消所有具有指定标签的能力。不会取消被忽略的实例
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

// 取消所有能力，无论标签如何。不会取消忽略的实例
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

// 取消所有能力并销毁任何剩余的实例能力
virtual void DestroyActiveState();
```

**注意：** 我发现如果你有`非实例化`的`GameplayAbilities`，`CancelAllAbilities`似乎无法正确工作。它似乎会影响到`非实例化`的`GameplayAbility`并中止。`CancelAbilities`可以更好地处理`非实例化`的`GameplayAbilities`，这也是示例项目的用法（跳跃是一个非实例化的`GameplayAbility`）。你的情况可能不同。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 获取主动技能
初学者经常问：“我如何获得主动技能？”也许是为了设置变量或取消它。一次可以激活多个`GameplayAbility`，因此没有一个“激活的能力”。相反，你必须在`ASC`的`ActivatableAbilities`列表中查找（这些是`ASC`拥有的已授予的`GameplayAbilities`），并找到与你想要的[`Asset`或`Granted` `GameplayTag`](#concepts-ga-tags)匹配的能力。

`UAbilitySystemComponent::GetActivatableAbilities()`返回一个`TArray<FGameplayAbilitySpec>`供你遍历。

`ASC`还有另一个辅助函数，它接受一个`GameplayTagContainer`作为参数，帮助你搜索，而不是手动遍历`GameplayAbilitySpecs`列表。`bOnlyAbilitiesThatSatisfyTagRequirements`参数将只返回满足`GameplayTag`要求并可以立即激活的`GameplayAbilitySpecs`。例如，你可能有两个基本攻击`GameplayAbilities`，一个使用武器，另一个使用空手，正确的能力会根据是否装备武器来激活，设置相应的`GameplayTag`要求。更多信息见Epic对该函数的评论。
```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

一旦你得到了你想要的`FGameplayAbilitySpec`，可以调用`IsActive()`来检查它是否处于激活状态。

**[⬆ 返回顶部](#table-of-contents)**



<a name="concepts-ga-instancing"></a>
#### 4.6.7 实例化策略
`GameplayAbility`的`实例化策略`决定了当能力激活时，是否以及如何实例化`GameplayAbility`。

| `实例化策略`               | 描述                                                                                           | 使用示例                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------ | ------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 每个Actor实例化           | 每个`ASC`只有一个`GameplayAbility`实例，该实例在每次激活时复用。                               | 这可能是你最常用的`实例化策略`。你可以将其用于任何能力，并且在激活之间提供持久性。设计师需要负责在每次激活之间手动重置任何需要的变量。                                                                                                                                                                                                                                                                                                                                                              |
| 每次执行实例化           | 每次激活`GameplayAbility`时，都会创建一个新的实例。                                            | 这些`GameplayAbilities`的优点是每次激活时变量都会被重置。但它们的性能较差，因为每次激活时都会生成新的`GameplayAbility`实例。示例项目中没有使用任何这种类型的能力。                                                                                                                                                                                                                                                                                                                   |
| 非实例化                 | `GameplayAbility`仅在其`类默认对象`上操作，不创建任何实例。                                     | 这是三种策略中性能最好的，但也是最受限的。`非实例化`的`GameplayAbility`不能存储状态，这意味着不能使用动态变量，也不能绑定`AbilityTask`委托。最佳用途是频繁使用的简单能力，如MOBA或RTS中的小兵基础攻击。示例项目中的跳跃`GameplayAbility`就是`非实例化`的。 |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 网络执行策略
`GameplayAbility`的`网络执行策略`决定了谁执行`GameplayAbility`以及执行的顺序。

| `网络执行策略`           | 描述                                                                                                                                                                          |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `仅本地`               | `GameplayAbility`只在拥有客户端上执行。对于只进行本地化外观更改的能力，这种方式非常有用。单人游戏应使用`仅服务器`。                                                |
| `本地预测`             | `本地预测`的`GameplayAbilities`首先在拥有客户端上激活，然后在服务器上执行。服务器的版本将修正客户端预测不准确的部分。请参见[预测](#concepts-p)。                            |
| `仅服务器`             | `GameplayAbility`只在服务器上执行。被动的`GameplayAbilities`通常是`仅服务器`。单人游戏应使用此选项。                                                                  |
| `服务器启动`           | `服务器启动`的`GameplayAbilities`首先在服务器上激活，然后在拥有客户端上执行。我个人很少使用这种策略。                                                                 |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 能力标签
`GameplayAbilities`包含有内建逻辑的`GameplayTagContainers`。这些`GameplayTags`不会进行复制。

| `GameplayTag容器`          | 描述                                                                                                                                                           |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `能力标签`                | `GameplayAbility`所拥有的`GameplayTags`。这些只是描述`GameplayAbility`的`GameplayTags`。                                                                 |
| `取消带标签的能力`         | 具有这些`GameplayTags`的其他`GameplayAbilities`将在此`GameplayAbility`激活时被取消。                                                                     |
| `阻止带标签的能力`         | 具有这些`GameplayTags`的其他`GameplayAbilities`在此`GameplayAbility`激活时将被阻止。                                                                   |
| `激活拥有标签`             | 这些`GameplayTags`会在`GameplayAbility`激活时分配给`GameplayAbility`的拥有者。请记住，这些标签不会被复制。                                                  |
| `激活所需标签`             | 只有当拥有者拥有**所有**这些`GameplayTags`时，才可以激活此`GameplayAbility`。                                                                          |
| `激活被阻止标签`           | 如果拥有者拥有**任何**这些`GameplayTags`，则无法激活此`GameplayAbility`。                                                                               |
| `源所需标签`               | 只有当`Source`拥有**所有**这些`GameplayTags`时，才能激活此`GameplayAbility`。`Source`的`GameplayTags`只有在`GameplayAbility`由事件触发时才会被设置。        |
| `源阻止标签`               | 如果`Source`拥有**任何**这些`GameplayTags`，则无法激活此`GameplayAbility`。`Source`的`GameplayTags`只有在`GameplayAbility`由事件触发时才会被设置。        |
| `目标所需标签`             | 只有当`Target`拥有**所有**这些`GameplayTags`时，才能激活此`GameplayAbility`。`Target`的`GameplayTags`只有在`GameplayAbility`由事件触发时才会被设置。      |
| `目标阻止标签`             | 如果`Target`拥有**任何**这些`GameplayTags`，则无法激活此`GameplayAbility`。`Target`的`GameplayTags`只有在`GameplayAbility`由事件触发时才会被设置。        |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 游戏能力规格（Gameplay Ability Spec）
`GameplayAbilitySpec`在`ASC`上存在，在`GameplayAbility`被授予后定义了可激活的`GameplayAbility` —— `GameplayAbility`类、等级、输入绑定以及必须与`GameplayAbility`类分开的运行时状态。

当`GameplayAbility`在服务器上被授予时，服务器会将`GameplayAbilitySpec`复制到拥有客户端，以便该客户端可以激活它。

激活`GameplayAbilitySpec`将根据其`实例化策略`创建一个实例（对于`非实例化`的`GameplayAbility`则不会创建实例）。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 向`Abilities`传递数据（Passing Data to Abilities）
`GameplayAbilities`的通用范式是`激活->生成数据->应用->结束`。有时你需要对现有数据进行操作。GAS提供了一些将外部数据传递给`GameplayAbilities`的选项：

| 方法                                              | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 通过事件激活`GameplayAbility`                     | 通过包含数据负载的事件激活`GameplayAbility`。事件的负载会从客户端复制到服务器，用于本地预测的`GameplayAbilities`。使用两个`Optional Object`或[`TargetData`](#concepts-targeting-data)变量传递任意数据，适用于现有变量无法适配的数据。这样做的缺点是，它会阻止通过输入绑定激活能力。要通过事件激活`GameplayAbility`，必须在`GameplayAbility`中设置它的`Triggers`。分配一个`GameplayTag`并选择`GameplayEvent`选项。要发送事件，请使用函数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。 |
| 使用`WaitGameplayEvent` `AbilityTask`              | 使用`WaitGameplayEvent` `AbilityTask`告诉`GameplayAbility`在激活后监听具有数据负载的事件。事件负载和发送过程与通过事件激活`GameplayAbilities`相同。这样做的缺点是，事件不会被`AbilityTask`复制，因此应仅用于`仅本地`和`仅服务器`的`GameplayAbilities`。你也可以编写自己的`AbilityTask`来复制事件负载。                                                                                                                                                                                                                                                                             |
| 使用`TargetData`                                  | 自定义的`TargetData`结构是一个很好的传递任意数据的方式，用于客户端和服务器之间。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| 将数据存储在`OwnerActor`或`AvatarActor`上       | 使用存储在`OwnerActor`、`AvatarActor`或任何可以引用的对象上的复制变量。这种方法最灵活，并且适用于通过输入绑定激活的`GameplayAbilities`。但是，它不能保证在使用时数据会同步，因为数据可能未及时复制。因此，如果你设置了一个复制变量，然后立即激活`GameplayAbility`，则无法保证复制顺序，这可能导致数据包丢失。                                                                                                                                           |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 技能消耗与冷却时间（Ability Cost and Cooldown）
`GameplayAbilities`具有可选的费用和冷却时间功能。费用是`ASC`必须拥有的预定义`Attributes`，用于激活带有`Instant` `GameplayEffect`的`GameplayAbility`（[`Cost GE`](#concepts-ge-cost)）。冷却时间是防止重新激活`GameplayAbility`的计时器，直到其过期，并通过`

Duration` `GameplayEffect`（[`Cooldown GE`](#concepts-ge-cooldown)）实现。

在`GameplayAbility`调用`UGameplayAbility::Activate()`之前，它会调用`UGameplayAbility::CanActivateAbility()`。此函数检查拥有的`ASC`是否可以支付费用（`UGameplayAbility::CheckCost()`）并确保`GameplayAbility`不在冷却中（`UGameplayAbility::CheckCooldown()`）。

在`GameplayAbility`调用`Activate()`之后，它可以随时选择使用`UGameplayAbility::CommitAbility()`提交费用和冷却时间，该函数会调用`UGameplayAbility::CommitCost()`和`UGameplayAbility::CommitCooldown()`。设计师可以选择分别调用`CommitCost()`或`CommitCooldown()`，如果它们不应同时提交。提交费用和冷却时间时，会再次调用`CheckCost()`和`CheckCooldown()`，这是`GameplayAbility`关于它们的最后一次检查。拥有`ASC`的`Attributes`可能会在`GameplayAbility`激活后发生变化，导致在提交时未能满足费用要求。如果`预测键`在提交时有效，提交费用和冷却时间可以进行[本地预测](#concepts-p)。

有关实现细节，请参见[`CostGE`](#concepts-ge-cost)和[`CooldownGE`](#concepts-ge-cooldown)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 能力升级（Leveling Up Abilities）
有两种常见的能力升级方法：

| 升级方法                                | 描述                                                                                                                                                                      |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 卸载并在新等级重新授予                 | 从`ASC`中卸载（移除）`GameplayAbility`，并在服务器上将其授予下一个等级。如果能力在此时已激活，则会终止该能力。                                                     |
| 增加`GameplayAbilitySpec`的等级        | 在服务器上，找到`GameplayAbilitySpec`，增加其等级，并标记为脏，以便复制到拥有的客户端。此方法不会终止正在激活的`GameplayAbility`。                                      |

两者的主要区别在于是否希望在升级时取消正在激活的`GameplayAbilities`。你可能会根据不同的`GameplayAbilities`使用两种方法。我建议为你的`UGameplayAbility`子类添加一个`bool`，指定使用哪种方法。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 能力集（Ability Sets）
`GameplayAbilitySets`是便捷的`UDataAsset`类，用于保存角色的输入绑定和启动的`GameplayAbilities`列表，并具有授予`GameplayAbilities`的逻辑。子类还可以包括额外的逻辑或属性。Paragon为每个英雄提供了一个`GameplayAbilitySet`，其中包括该英雄的所有`GameplayAbilities`。

根据我看到的内容，我认为这个类没有必要。示例项目将所有`GameplayAbilitySets`的功能都包含在`GDCharacterBase`及其子类中。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 能力批处理（Ability Batching）
传统的`GameplayAbility`生命周期涉及至少两到三个RPC从客户端发送到服务器。

1. `CallServerTryActivateAbility()`
2. `ServerSetReplicatedTargetData()`（可选）
3. `ServerEndAbility()`

如果`GameplayAbility`在一个帧内将所有这些操作打包成一个原子分组，我们可以优化这个工作流，将这两到三个RPC合并成一个RPC。GAS称这种RPC优化为`Ability Batching`。使用`Ability Batching`的常见示例是命中扫描枪。命中扫描枪激活后，进行线性追踪，将[`TargetData`](#concepts-targeting-data)发送到服务器，然后结束该能力，所有这些操作都在一个原子分组内完成。 [GASShooter](https://github.com/tranek/GASShooter)示例项目展示了这种技术用于其命中扫描枪。

半自动枪是最好的例子，它将`CallServerTryActivateAbility()`、`ServerSetReplicatedTargetData()`（子弹命中结果）和`ServerEndAbility()`批处理成一个RPC，而不是三个RPC。

全自动/爆发枪将`CallServerTryActivateAbility()`和`ServerSetReplicatedTargetData()`批处理成一个RPC，而每个后续子弹则为单独的`ServerSetReplicatedTargetData()` RPC。最后，`ServerEndAbility()`作为单独的RPC发送，当枪支停止射击时。这是最坏的情况，我们仅保存第一个子弹的一个RPC，而不是两个RPC。这个场景也可以通过使用[`GameplayEvent`](#concepts-ga-data)激活能力来实现，这将会把子弹的`TargetData`与`EventPayload`一起发送到服务器。后者的缺点是，`TargetData`必须在能力之外生成，而批处理方法则在能力内部生成`TargetData`。

默认情况下，`Ability Batching`在[`ASC`](#concepts-asc)上是禁用的。要启用`Ability Batching`，需要重写`ShouldDoServerAbilityRPCBatch()`并返回true：

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

启用`Ability Batching`后，在激活要批处理的能力之前，必须先创建一个`FScopedServerAbilityRPCBatcher`结构体。这个特殊结构会尝试在其作用域内批处理随后的所有能力。一旦`FScopedServerAbilityRPCBatcher`超出作用域，任何激活的能力将不会再尝试批处理。`FScopedServerAbilityRPCBatcher`通过在每个可以批处理的函数中插入特殊代码来工作，它会拦截RPC调用并将消息打包进批处理结构体。当`FScopedServerAbilityRPCBatcher`超出作用域时，它会自动将这个批处理结构体RPC发送到服务器，方法是`UAbilitySystemComponent::EndServerAbilityRPCBatch()`。服务器通过`UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)`接收批处理RPC。`BatchInfo`参数包含是否结束能力的标志，以及激活时是否按下了输入的标志，还有`TargetData`（如果包括的话）。这是一个确认批处理是否正常工作好的函数，可以设置断点调试。或者，可以使用控制台变量`AbilitySystem.ServerRPCBatching.Log 1`来启用专门的能力批处理日志记录。

这个机制只能在C++中执行，并且只能通过`FGameplayAbilitySpecHandle`激活能力。

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
    bool AbilityActivated = false;
    if (InAbilityHandle.IsValid())
    {
        FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
        AbilityActivated = TryActivateAbility(InAbilityHandle, true);

        if (EndAbilityImmediately)
        {
            FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
            if (AbilitySpec)
            {
                UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
                GSAbility->ExternalEndAbility();
            }
        }

        return AbilityActivated;
    }

    return AbilityActivated;
}
```

GASShooter复用了同一个批处理`GameplayAbility`用于半自动和全自动枪，而不直接调用`EndAbility()`（它由一个本地唯一的能力管理玩家输入，并根据当前射击模式调用批处理能力）。由于所有RPC都必须在`FScopedServerAbilityRPCBatcher`的作用域内发生，因此提供了`EndAbilityImmediately`参数，便于控制/管理本地唯一的能力指定是否应该批处理`EndAbility()`调用（半自动），或者不批处理`EndAbility()`调用（全自动），并且`EndAbility()`调用将在稍后通过单独的RPC执行。

GASShooter暴露了一个Blueprint节点来允许批处理能力，前述的本地唯一能力使用它来触发批处理能力。

![激活批处理能力](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 网络安全策略
一个 `GameplayAbility` 的 `NetSecurityPolicy` 决定了能力应该在网络上在哪里执行。它提供了防止客户端试图执行受限能力的保护。

| `NetSecurityPolicy`     | 描述                                                                                                                                       |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `ClientOrServer`        | 没有安全要求。客户端或服务器可以自由触发此能力的执行和终止。                                                                                   |
| `ServerOnlyExecution`   | 服务器会忽略客户端请求执行此能力。客户端仍然可以请求服务器取消或结束此能力。                                                                  |
| `ServerOnlyTermination` | 服务器会忽略客户端请求取消或结束此能力。客户端仍然可以请求执行此能力。                                                                       |
| `ServerOnly`            | 服务器控制此能力的执行和终止。客户端的任何请求都会被忽略。                                                                                   |

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 能力任务

<a name="concepts-at-definition"></a>
### 4.7.1 能力任务定义
`GameplayAbilities` 只在一帧内执行，这本身没有太多灵活性。为了执行那些需要持续时间或响应未来某个时刻触发的委托的动作，我们使用称为 `AbilityTasks` 的延迟动作。

GAS 提供了许多内置的 `AbilityTasks`：
* 用 `RootMotionSource` 移动角色的任务
* 播放动画蒙太奇的任务
* 响应 `Attribute` 变化的任务
* 响应 `GameplayEffect` 变化的任务
* 响应玩家输入的任务
* 等等

`UAbilityTask` 构造函数强制执行一个硬编码的游戏全局最大值：1000 个并发的 `AbilityTasks` 同时运行。在为像 RTS 游戏那样可以同时拥有数百个角色的游戏设计 `GameplayAbilities` 时，需要记住这一点。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-at-definition"></a>
### 4.7.2 自定义能力任务
通常你会创建自己的自定义 `AbilityTasks`（在 C++ 中）。示例项目包含两个自定义 `AbilityTasks`：
1. `PlayMontageAndWaitForEvent` 是默认的 `PlayMontageAndWait` 和 `WaitGameplayEvent` `AbilityTasks` 的组合。它允许动画蒙太奇通过 `AnimNotifies` 从动画通知中发送游戏事件回到启动它们的 `GameplayAbility`。使用这个可以在动画蒙太奇中的特定时间触发动作。
2. `WaitReceiveDamage` 监听 `OwnerActor` 是否受到伤害。被动护甲堆叠的 `GameplayAbility` 在英雄受到伤害时移除一层护甲。

`AbilityTasks` 由以下部分组成：
* 创建新的 `AbilityTask` 实例的静态函数
* 在 `AbilityTask` 完成其目的时广播的委托
* 启动其主要任务、绑定外部委托等的 `Activate()` 函数
* 清理工作的 `OnDestroy()` 函数，包括它绑定的外部委托
* 绑定到的任何外部委托的回调函数
* 成员变量和任何内部辅助函数

**注意：** `AbilityTasks` 只能声明一种类型的输出委托。所有输出委托必须是这种类型，无论它们是否使用参数。如果不使用委托参数，请传递默认值。

`AbilityTasks` 只在运行拥有 `GameplayAbility` 的客户端或服务器上运行；但是，`AbilityTasks` 可以通过在 `AbilityTask` 构造函数中设置 `bSimulatedTask = true;`，覆盖 `virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`，并将任何成员变量设置为复制来设置为在模拟客户端上运行。这在像移动 `AbilityTasks` 这样的少数特殊情况中很有用，其中你不想复制每个移动变化，而是模拟整个移动 `AbilityTask`。所有 `RootMotionSource` `AbilityTasks` 都这么做。参考 `AbilityTask_MoveToLocation.h/.cpp` 作为示例。

如果在 `AbilityTask` 构造函数中设置 `bTickingTask = true;` 并覆盖 `virtual void TickTask(float DeltaTime);`，`AbilityTasks` 可以进行每帧的更新。这在你需要平滑地跨帧插值值时非常有用。参考 `AbilityTask_MoveToLocation.h/.cpp` 作为示例。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-at-using"></a>
### 4.7.3 使用能力任务
在 C++ 中创建和激活一个 `AbilityTask`（来自 `GDGA_FireGun.cpp`）：
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

在蓝图中，我们只需使用为 `AbilityTask` 创建的蓝图节点。我们不必调用 `ReadyForActivation()`，因为它会被 `Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp` 自动调用。`K2Node_LatentGameplayTaskCall` 还会自动调用 `BeginSpawningActor()` 和 `FinishSpawningActor()`（如果它们在你的 `AbilityTask` 类中存在）（参见 `AbilityTask_WaitTargetData`）。重申一下，`K2Node_LatentGameplayTaskCall` 只是对蓝图做了自动魔法处理。在 C++ 中，我们必须手动调用 `ReadyForActivation()`、`BeginSpawningActor()` 和 `FinishSpawningActor()`。

![蓝图 WaitTargetData 能力任务](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

要手动取消一个 `AbilityTask`，只需在蓝图（称为 `Async Task Proxy`）或 C++ 中调用 `EndTask()`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-at-rms"></a>
### 4.7.4 根运动源能力任务
GAS 提供了 `AbilityTasks`，用于通过 `Root Motion Sources` 在 `CharacterMovementComponent` 中移动角色，适用于击退、复杂跳跃、拉动和冲刺等操作。

**注意：** 预测 `RootMotionSource` `AbilityTasks` 适用于 4.19 和 4.25+ 版本的引擎。对于 4.20-4.24 版本的引擎，预测存在 bug；然而，`AbilityTasks` 在多人游戏中仍能正常执行，尽管会有轻微的网络修正，在单人游戏中表现完美。可以从 4.25 版本中挑选 [预测修复](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c) 到自定义的 4.20-4.24 引擎中。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 游戏提示

<a name="concepts-gc-definition"></a>
#### 4.8.1 游戏提示定义
`GameplayCues`（简称 `GC`）用于执行与游戏玩法无关的内容，如音效、粒子效果、摄像机震动等。`GameplayCues` 通常是复制的（除非显式地在本地执行 `Executed`、`Added` 或 `Removed`），并且是预测的。

我们通过发送相应的 `GameplayTag`（必须具有父标签 `GameplayCue.`）和事件类型（`Execute`、`Add` 或 `Remove`）到 `GameplayCueManager` 来触发 `GameplayCues`，该操作通过 `ASC` 实现。`GameplayCueNotify` 对象和其他实现了 `IGameplayCueInterface` 的 `Actor` 可以根据 `GameplayCue` 的 `GameplayTag`（`GameplayCueTag`）订阅这些事件。

**注意：** 重申一下，`GameplayCue` 的 `GameplayTags` 必须以父标签 `GameplayCue` 开头。例如，有效的 `GameplayCue` `GameplayTag` 可能是 `GameplayCue.A.B.C`。

`GameplayCueNotifies` 分

为两类：`Static` 和 `Actor`。它们响应不同的事件，且不同类型的 `GameplayEffect` 可以触发它们。重写相应的事件并实现你的逻辑。

| `GameplayCue` 类                                                                                                                   | 事件               | `GameplayEffect` 类型   | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ---------------------------------------------------------------------------------------------------------------------------------- | ------------------ | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`          | `Instant` 或 `Periodic` | 静态 `GameplayCueNotifies` 在 `ClassDefaultObject` 上操作（即没有实例），非常适合用于一次性效果，比如击中冲击。                                                                                                                                                                                                                                                                                                                                                                                      |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                       | `Add` 或 `Remove`  | `Duration` 或 `Infinite` | Actor 类型的 `GameplayCueNotifies` 在 `Add` 时会生成一个新实例。由于这些是实例化的，它们可以持续执行直到被 `Remove`。它们适合循环音效和粒子效果，当相应的 `Duration` 或 `Infinite` 类型的 `GameplayEffect` 被移除时，或者通过手动调用移除时。它们还带有选项来管理允许同时 `Add` 的数量，避免相同效果的多次应用只启动一次音效或粒子效果。 |

`GameplayCueNotifies` 理论上可以响应任何事件，但这通常是我们如何使用它们的方式。

**注意：** 使用 `GameplayCueNotify_Actor` 时，检查 `Auto Destroy on Remove`，否则后续调用 `Add` 相同的 `GameplayCueTag` 时将无法生效。

当使用 `ASC` 的 [复制模式](#concepts-asc-rm) 为非 `Full` 模式时，`Add` 和 `Remove` 的 `GC` 事件会在服务器玩家（监听服务器）上触发两次——一次是应用 `GE`，另一次是从 "Minimal" 的 `NetMultiCast` 发送到客户端。然而，`WhileActive` 事件仍然只会触发一次。所有事件只会在客户端触发一次。

示例项目中包含了一个 `GameplayCueNotify_Actor` 用于眩晕和冲刺效果，还包含了一个 `GameplayCueNotify_Static` 用于 FireGun 的射击冲击。这些 `GC` 可以通过 [本地触发](#concepts-gc-local)进行进一步优化，而不是通过 `GE` 进行复制。在示例项目中，我选择展示初学者使用的方式。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 触发游戏提示

当 `GameplayEffect` 成功应用时（未被标签或免疫阻止），填写应触发的所有 `GameplayCue` 的 `GameplayTags`。

![从 GameplayEffect 触发的 GameplayCue](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` 提供了蓝图节点来 `Execute`、`Add` 或 `Remove` `GameplayCues`。

![从 GameplayAbility 触发的 GameplayCue](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

在 C++ 中，你可以直接在 `ASC` 上调用这些函数（或者在你的 `ASC` 子类中暴露它们给蓝图）：

```c++
/** 游戏提示也可以独立触发。这些可以带有一个可选的效果上下文来传递击中结果等 */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 添加一个持久的游戏提示 */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 移除一个持久的游戏提示 */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** 移除任何独立添加的游戏提示，即不是作为 `GameplayEffect` 一部分的游戏提示 */
void RemoveAllGameplayCues();
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 本地游戏提示
从 `GameplayAbilities` 和 `ASC` 触发的游戏提示默认是复制的。每个 `GameplayCue` 事件是一个多播 RPC。这会导致大量的 RPC。GAS 还强制每个网络更新最多只允许有两个相同的 `GameplayCue` RPC。为了避免这一点，我们在可能的情况下使用本地的 `GameplayCues`。本地的 `GameplayCues` 仅在单独的客户端上执行 `Execute`、`Add` 或 `Remove`。

可以使用本地 `GameplayCues` 的场景：
* 投射物冲击
* 近战碰撞冲击
* 从动画蒙太奇触发的 `GameplayCues`

你应该将以下本地 `GameplayCue` 函数添加到你的 `ASC` 子类：

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

如果一个 `GameplayCue` 是本地 `Add` 的，它应该本地 `Remove`。如果它是通过复制 `Add` 的，它应该通过复制 `Remove`。

**[⬆ 返回顶部](#table-of-contents)**

 
<a name="concepts-gc-parameters"></a>  
   
#### 4.8.4 Gameplay Cue 参数  
   
`GameplayCues` 接收一个 `FGameplayCueParameters` 结构，包含作为参数传递给 `GameplayCue` 的额外信息。如果你从 `GameplayAbility` 或 `ASC` 的函数中手动触发 `GameplayCue`，那么你必须手动填写传递给 `GameplayCue` 的 `GameplayCueParameters` 结构。如果 `GameplayCue` 是由 `GameplayEffect` 触发的，那么以下变量会自动填充到 `GameplayCueParameters` 结构中：  
   
* AggregatedSourceTags  
* AggregatedTargetTags  
* GameplayEffectLevel  
* AbilityLevel  
* [EffectContext](#concepts-ge-context)  
* Magnitude（如果 `GameplayEffect` 在 `GameplayCue` 标签容器上选择了一个用于幅度的 `Attribute` 并且有一个相应的影响该 `Attribute` 的 `Modifier`）  
   
在手动触发 `GameplayCue` 时，`GameplayCueParameters` 结构中的 `SourceObject` 变量可能是传递任意数据给 `GameplayCue` 的一个好地方。  
   
**注意：** 参数结构中的一些变量如 `Instigator` 可能已经存在于 `EffectContext` 中。`EffectContext` 也可以包含一个 `FHitResult`，用于在世界中生成 `GameplayCue` 的位置。子类化 `EffectContext` 可能是向 `GameplayCues` 传递更多数据的好方法，特别是那些由 `GameplayEffect` 触发的。  
   
参见 [`UAbilitySystemGlobals`](#concepts-asg) 中的 3 个函数，它们填充 `GameplayCueParameters` 结构以获取更多信息。它们是虚拟的，所以你可以重写它们以自动填充更多信息。  
   
```c++  
/** Initialize GameplayCue Parameters */  
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);  
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);  
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);  
```  
   
**[⬆ 回到顶部](#table-of-contents)**  
   
<a name="concepts-gc-manager"></a>  
   
#### 4.8.5 Gameplay Cue 管理器  
   
默认情况下，`GameplayCueManager` 将扫描整个游戏目录中的 `GameplayCueNotifies` 并在播放时将它们加载到内存中。我们可以通过在 `DefaultGame.ini` 中设置 `GameplayCueManager` 的扫描路径来改变扫描路径。  
   
```ini  
[/Script/GameplayAbilities.AbilitySystemGlobals]  
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"  
```  
   
我们希望 `GameplayCueManager` 扫描并找到所有的 `GameplayCueNotifies`；然而，我们不希望它在播放时异步加载每一个。这会把每个 `GameplayCueNotify` 及其所有引用的声音和粒子加载到内存中，无论它们是否在关卡中使用。在一个像 Paragon 这样的大型游戏中，这可能会在内存中造成数百兆字节的不必要资产，并导致启动时的卡顿和游戏冻结。  
   
一种替代在启动时异步加载每个 `GameplayCue` 的方法是只在游戏中触发时异步加载 `GameplayCues`。这样可以缓解不必要的内存使用和在异步加载每个 `GameplayCue` 时可能造成的游戏硬冻结，以交换在首次触发特定 `GameplayCue` 时可能的效果延迟。对于 SSD 来说，这种潜在延迟是不存在的。我没有在 HDD 上测试过。如果在 UE 编辑器中使用此选项，GameplayCues 第一次加载时，如果编辑器需要编译粒子系统，可能会有轻微的卡顿或冻结。在构建版本中，这不是问题，因为粒子系统已经编译好。  
   
首先我们必须子类化 `UGameplayCueManager` 并在 `DefaultGame.ini` 中告诉 `AbilitySystemGlobals` 类使用我们的 `UGameplayCueManager` 子类。  
   
```ini  
[/Script/GameplayAbilities.AbilitySystemGlobals]  
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"  
```  
   
在我们的 `UGameplayCueManager` 子类中，重写 `ShouldAsyncLoadRuntimeObjectLibraries()`。  
   
```c++  
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override  
{  
    return false;  
}  
```  
   
**[⬆ 回到顶部](#table-of-contents)**  
   
<a name="concepts-gc-prevention"></a>  
   
#### 4.8.6 防止 Gameplay Cues 触发  
   
有时我们不希望 `GameplayCues` 被触发。例如，如果我们阻止了一次攻击，我们可能不希望播放附加到伤害 `GameplayEffect` 的命中效果，或者希望播放一个自定义的效果。我们可以通过在 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 中调用 `OutExecutionOutput.MarkGameplayCuesHandledManually()` 来实现这一点，然后手动将我们的 `GameplayCue` 事件发送到 `Target` 或 `Source` 的 `ASC`。  
   
如果你永远不希望某个特定的 `ASC` 上的任何 `GameplayCues` 被触发，你可以设置 `AbilitySystemComponent->bSuppressGameplayCues = true;`。  
   
**[⬆ 回到顶部](#table-of-contents)**  
   
<a name="concepts-gc-batching"></a>  
   
#### 4.8.7 Gameplay Cue 批处理  
   
每个触发的 `GameplayCue` 都是一个不可靠的 NetMulticast RPC。在我们同时触发多个 `GCs` 的情况下，有一些优化方法可以将它们压缩为一个 RPC 或通过发送更少的数据来节省带宽。  
   
<a name="concepts-gc-batching-manualrpc"></a>  
   
##### 4.8.7.1 手动 RPC  
   
假设你有一把发射八个弹丸的霰弹枪。这是八个轨迹和冲击 `GameplayCues`。[GASShooter](https://github.com/tranek/GASShooter) 采用了一种懒惰的方法，将它们组合成一个 RPC，通过将所有的轨迹信息藏入 [`EffectContext`](#concepts-ge-ec) 作为 [`TargetData`](#concepts-targeting-data)。虽然这将 RPC 从八个减少到一个，但它仍然在一个 RPC 中发送了大量数据（大约 500 字节）。一种更优化的方法是发送一个带有自定义结构的 RPC，在其中有效地编码命中位置，或者你可以给它一个随机种子号，以在接收端重现/近似冲击位置。客户端然后解包这个自定义结构并转化为[本地执行的 `GameplayCues`](#concepts-gc-local)。  
   
这样运作：  
   
1. 声明一个 `FScopedGameplayCueSendContext`。这会抑制 `UGameplayCueManager::FlushPendingCues()` 直到它超出范围，这意味着所有 `GameplayCues` 将会被排队直到 `FScopedGameplayCueSendContext` 超出范围。  
2. 重写 `UGameplayCueManager::FlushPendingCues()` 以根据一些自定义 `GameplayTag` 将可以批处理在一起的 `GameplayCues` 合并到你的自定义结构中并将其 RPC 到客户端。  
3. 客户端接收自定义结构并解包成本地执行的 `GameplayCues`。  
   
此方法也可用于当你需要特定参数来适应 `GameplayCues`，但不适合 `GameplayCueParameters` 提供的内容，并且你不希望将它们添加到 `EffectContext` 中，如伤害数字、暴击指示器、破盾指示器、致命命中指示器等。  
   
https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager  
   
<a name="concepts-gc-batching-gcsonge"></a>  
   
##### 4.8.7.2 一个 GE 上的多个 GCs  
   
所有在 `GameplayEffect` 上的 `GameplayCues` 都已经在一个 RPC 中发送。默认情况下，`UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()` 将发送整个 `GameplayEffectSpec`（但转换为 `FGameplayEffectSpecForRPC`），即使在 `ASC` 的 `Replication Mode` 下也是不可靠的。这可能会消耗大量带宽，具体取决于 `GameplayEffectSpec` 中的内容。我们可以通过设置 cvar `AbilitySystem.AlwaysConvertGESpecToGCParams 1` 来优化这一点。这将 `GameplayEffectSpecs` 转换为 `FGameplayCueParameter` 结构并 RPC 这些而不是整个 `FGameplayEffectSpecForRPC`。这可能会节省带宽，但也会有更少的信息，这取决于 `GESpec` 如何转换为 `GameplayCueParameters` 以及你的 `GCs` 需要知道什么。  
   
**[⬆ 回到顶部](#table-of-contents)**  
   
<a name="concepts-gc-events"></a>  
   
#### 4.8.8 Gameplay Cue 事件  
   
`GameplayCues` 响应特定的 `EGameplayCueEvents`：  
   
| `EGameplayCueEvent` | 描述 |  
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |  
| `OnActive`          | 当 `GameplayCue` 被激活（添加）时调用。 |  
| `WhileActive`       | 当 `GameplayCue` 处于活动状态时调用，即使它实际上并没有刚刚被应用（例如进行中加入等）。这不是 `Tick`！当 `GameplayCueNotify_Actor` 被添加或变得相关时，它会像 `OnActive` 一样被调用一次。如果需要 `Tick()`，只需使用 `GameplayCueNotify_Actor` 的 `Tick()`。毕竟它是一个 `AActor`。 |  
| `Removed`           | 当 `GameplayCue` 被移除时调用。响应此事件的 Blueprint `GameplayCue` 函数是 `OnRemove`。 |  
| `Executed`          | 当 `GameplayCue` 被执行时调用：即时效果或周期性 `Tick()`。响应此事件的 Blueprint `GameplayCue` 函数是 `OnExecute`。 |  
   
使用 `OnActive` 处理在 `GameplayCue` 开始时发生的任何事情，但如果迟到的加入者错过也没关系。使用 `WhileActive` 处理 `GameplayCue` 中的持续效果，你希望迟到的加入者看到。例如，如果你有一个 MOBA 中塔结构爆炸的 `GameplayCue`，你会把初始爆炸粒子系统和爆炸声放在 `OnActive` 中，而任何残留的持续火焰粒子或声音放在 `WhileActive` 中。在这种情况下，迟到的加入者重播来自 `OnActive` 的初始爆炸是没有意义的，但你会希望他们在爆炸发生后从 `WhileActive` 看到地面上的持续循环火焰效果。`OnRemove` 应该清理在 `OnActive` 和 `WhileActive` 中添加的任何东西。每次 Actor 进入 `GameplayCueNotify_Actor` 的相关范围时，`WhileActive` 将被调用。每次 Actor 离开 `GameplayCueNotify_Actor` 的相关范围时，`OnRemove` 将被调用。  
   
**[⬆ 回到顶部](#table-of-contents)**  
   
<a name="concepts-gc-reliability"></a>  
   
#### 4.8.9 Gameplay Cue 可靠性  
   
一般来说，`GameplayCues` 应被认为是不可靠的，因此不适合直接影响游戏玩法的任何事情。  
   
**Executed `GameplayCues`:** 这些 `GameplayCues` 是通过不可靠的多播应用的，并且始终是不可靠的。  
   
**`GameplayCues` 应用于 `GameplayEffects`:**  
   
* 自主代理可靠接收 `OnActive`、`WhileActive` 和 `OnRemove`。`FActiveGameplayEffectsContainer::NetDeltaSerialize()` 调用 `UAbilitySystemComponent::HandleDeferredGameplayCues()` 来调用 `OnActive` 和 `WhileActive`。`FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` 调用 `OnRemoved`。  
* 模拟代理可靠接收 `WhileActive` 和 `OnRemove`。`UAbilitySystemComponent::MinimalReplicationGameplayCues` 的复制调用 `WhileActive` 和 `OnRemove`。`OnActive` 事件是通过不可靠多播调用的。  
   
**`GameplayCues` 未通过 `GameplayEffect` 应用:**  
   
* 自主代理可靠接收 `OnRemove`。`OnActive` 和 `WhileActive` 事件是通过不可靠多播调用的。  
* 模拟代理可靠接收 `WhileActive` 和 `OnRemove`。`UAbilitySystemComponent::MinimalReplicationGameplayCues` 的复制调用 `WhileActive` 和 `OnRemove`。`OnActive` 事件是通过不可靠多播调用的。  
   
如果你需要 `GameplayCue` 中的某些东西是“可靠的”，那么从 `GameplayEffect` 应用它，并使用 `WhileActive` 添加效果，使用 `OnRemove` 移除效果。  
   
**[⬆ 回到顶部](#table-of-contents)**  
   
<a name="concepts-asg"></a>  
   
### 4.9 能力系统全局设置  
   
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) 类持有关于 GAS 的全局信息。大多数变量可以从 `DefaultGame.ini` 中设置。一般来说，你不需要与这个类交互，但你应该知道它的存在。如果你需要子类化像 [`GameplayCueManager`](#concepts-gc-manager) 或 [`GameplayEffectContext`](#concepts-ge-context) 这样的东西，你必须通过 `AbilitySystemGlobals` 来实现。  
   
要子类化 `AbilitySystemGlobals`，在 `DefaultGame.ini` 中设置类名：  
   
```ini  
[/Script/GameplayAbilities.AbilitySystemGlobals]  
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"  
```  
   
<a name="concepts-asg-initglobaldata"></a>  
   
#### 4.9.1 InitGlobalData()  
   
在 UE 4.24 到 5.2 之间，需要调用 `UAbilitySystemGlobals::Get().InitGlobalData()` 来使用 [`TargetData`](#concepts-targeting-data)，否则你会遇到与 `ScriptStructCache` 相关的错误，并且客户端会从服务器断开连接。此函数在项目中只需调用一次。Fortnite 从 `UAssetManager::StartInitialLoading()` 调用它，而 Paragon 从 `UEngine::Init()` 调用它。我发现把它放在 `UAssetManager::StartInitialLoading()` 是一个不错的地方，如示例项目中所示。我会将此视为样板代码，你应该复制到项目中以避免与 `TargetData` 相关的问题。从 5.3 开始，它会自动调用。  
   
如果你在使用 `AbilitySystemGlobals` 的 `GlobalAttributeSetDefaultsTableNames` 时遇到崩溃，你可能需要稍后调用 `UAbilitySystemGlobals::Get().InitGlobalData()`，例如在 `AssetManager` 中，或者在 `GameInstance` 中。  
   
**[⬆ 回到顶部](#table-of-contents)**  

<a name="concepts-p"></a>  
   
### 4.10 预测  
   
GAS 开箱即用地支持客户端预测；然而，它并不会预测所有内容。在 GAS 中，客户端预测意味着客户端无需等待服务器的许可即可激活 `GameplayAbility` 并应用 `GameplayEffects`。它可以“预测”服务器允许其执行此操作，并预测将 `GameplayEffects` 应用于的目标。然后，服务器在客户端激活后运行 `GameplayAbility` 的网络延迟时间，并告知客户端其预测是否正确。如果客户端在任何预测中出错，它将“回滚”其“错误预测”中的更改以匹配服务器。  
   
关于 GAS 相关预测的权威来源是插件源代码中的 `GameplayPrediction.h`。  
   
Epic 的心态是只预测你可以“逃脱”的东西。例如，Paragon 和 Fortnite 并不预测伤害。他们很可能使用 [`ExecutionCalculations`](#concepts-ge-ec) 来计算其伤害，这无论如何都无法预测。这并不是说你不能尝试预测某些事情，如伤害。如果你这样做并且效果很好，那就太好了。  
   
> ... 我们并没有“无缝且自动”地预测所有内容。我们仍然觉得玩家预测最好保持在最低限度（意思是：预测你可以“逃脱”的最小量的东西）。  
> *来自 Epic 的 Dave Ratti 的评论，摘自新的 [Network Prediction Plugin](#concepts-p-npp)*  
   
**预测的内容：**  
> * 能力激活  
> * 触发事件  
> * `GameplayEffect` 应用：  
>   * 属性修改（例外：执行不进行预测，只有属性修改器）  
>   * `GameplayTag` 修改  
> * `Gameplay Cue` 事件（无论是在预测的游戏效果中还是单独）  
> * 蒙太奇  
> * 移动（内置于 UE 的 `UCharacterMovement`）  
   
**未预测的内容：**  
> * `GameplayEffect` 移除  
> * `GameplayEffect` 的周期性效果（如持续伤害）  
   
*摘自 `GameplayPrediction.h`*  
   
虽然我们可以预测 `GameplayEffect` 的应用，但我们无法预测其移除。我们可以通过在想要移除 `GameplayEffect` 时预测逆向效果来解决此限制。比如说我们预测了移动速度降低 40%。我们可以通过应用 40% 的移动速度增益来预测性地移除它，然后同时移除两个 `GameplayEffects`。这并不适用于每种情况，仍然需要支持预测 `GameplayEffect` 的移除。Epic 的 Dave Ratti 表示希望在 [GAS 的未来迭代](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)中添加此功能。  
   
因为我们无法预测 `GameplayEffects` 的移除，所以我们无法完全预测 `GameplayAbility` 的冷却时间，并且没有逆向 `GameplayEffect` 的解决方案。服务器复制的 `Cooldown GE` 会存在于客户端，并且任何尝试绕过此复制（例如使用 `Minimal` 复制模式）都会被服务器拒绝。这意味着延迟较高的客户端需要更长时间告诉服务器进入冷却状态，并接收服务器的 `Cooldown GE` 的移除。也就是说，延迟较高的玩家的射击频率将低于延迟较低的玩家，这会使他们在与延迟较低的玩家对抗时处于劣势。Fortnite 通过使用自定义的账簿记录而不是 `Cooldown GEs` 来避免这个问题。  
   
关于预测伤害，我个人不推荐尽管这是大多数人在开始使用 GAS 时尝试的第一件事。我尤其不推荐尝试预测死亡。虽然你可以预测伤害，但这样做很棘手。如果你错误地预测了伤害，玩家会看到敌人的健康值回升。这在你尝试预测死亡时尤其尴尬和令人沮丧。假如你错误地预测了 `Character` 的死亡，它开始软体化，但当服务器纠正它时，它会停止软体化并继续向你射击。  
   
**注意：** `Instant` `GameplayEffects`（如 `Cost GEs`）改变 `Attributes` 可以在自己身上无缝预测，预测对其他角色的 `Instant` `Attribute` 更改将显示短暂的异常或“闪烁”。预测的 `Instant` `GameplayEffects` 实际上被视为 `Infinite` `GameplayEffects`，以便在错误预测时可以回滚。当服务器的 `GameplayEffect` 被应用时，可能存在两个相同的 `GameplayEffect` 导致 `Modifier` 被应用两次或在短时间内根本没有应用。最终它会自行纠正，但有时玩家会注意到闪烁。  
   
GAS 预测实现试图解决的问题：  
> 1. “我能做到吗？” 预测的基本协议。  
> 2. “撤销” 当预测失败时如何撤销副作用。  
> 3. “重做” 如何避免重放我们在本地预测但也从服务器复制的副作用。  
> 4. “完整性” 如何确保我们/真正/预测了所有副作用。  
> 5. “依赖性” 如何管理依赖的预测和预测事件链。  
> 6. “覆盖” 如何预测性地覆盖状态，否则由服务器复制/拥有。  
   
*摘自 `GameplayPrediction.h`*  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="concepts-p-key"></a>  
   
#### 4.10.1 预测键  
   
GAS 的预测基于 `Prediction Key` 的概念，它是客户端在激活 `GameplayAbility` 时生成的整数标识符。  
   
* 客户端在激活 `GameplayAbility` 时生成一个预测键。这是 `Activation Prediction Key`。  
* 客户端通过 `CallServerTryActivateAbility()` 将此预测键发送到服务器。  
* 客户端将此预测键添加到其应用的所有 `GameplayEffects` 中，直到预测键有效。  
* 客户端的预测键失效。在同一 `GameplayAbility` 中的进一步预测效果需要一个新的 [Scoped Prediction Window](#concepts-p-windows)。  
   
* 服务器接收来自客户端的预测键。  
* 服务器将此预测键添加到其应用的所有 `GameplayEffects` 中。  
* 服务器将预测键复制回客户端。  
   
* 客户端接收来自服务器的带有用于应用它们的预测键的复制 `GameplayEffects`。如果任何复制的 `GameplayEffects` 与客户端应用的具有相同预测键的 `GameplayEffects` 匹配，则它们被正确预测。目标上将暂时存在两个 `GameplayEffect` 的副本，直到客户端移除其预测的副本。  
* 客户端从服务器接收预测键。这是 `Replicated Prediction Key`。此预测键现在标记为陈旧。  
* 客户端移除**所有**它创建的带有现已陈旧的复制预测键的 `GameplayEffects`。服务器复制的 `GameplayEffects` 将保留。客户端添加的任何 `GameplayEffects` 且没有接收到来自服务器的匹配复制版本的被错误预测。  
   
预测键保证在 `GameplayAbilities` 中的指令“窗口”中从 `Activation` 开始的原子分组期间有效。你可以将其理解为仅在一帧期间有效。任何来自潜在动作 `AbilityTasks` 的回调将不再有有效的预测键，除非 `AbilityTask` 内置同步点生成新的 [Scoped Prediction Window](#concepts-p-windows)。  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="concepts-p-windows"></a>  
   
#### 4.10.2 在能力中创建新的预测窗口  
   
要预测 `AbilityTasks` 中回调的更多操作，我们需要使用新的 Scoped Prediction Key 创建新的 Scoped Prediction Window。这有时被称为客户端和服务器之间的同步点。一些 `AbilityTasks`，如所有与输入相关的任务，都具有内置功能以创建新的 Scoped Prediction Window，这意味着 `AbilityTasks` 回调中的原子代码具有有效的 Scoped Prediction Key 可供使用。其他任务，如 `WaitDelay` 任务，没有内置代码为其回调创建新的 Scoped Prediction Window。如果你需要在没有内置代码的 `AbilityTask` 后预测动作以创建 Scoped Prediction Window，如 `WaitDelay`，我们必须手动使用选项 `OnlyServerWait` 的 `WaitNetSync` `AbilityTask` 来完成。当客户端遇到 `WaitNetSync` 并选择 `OnlyServerWait` 时，它基于 `GameplayAbility` 的激活预测键生成一个新的 Scoped Prediction Key，RPC 到服务器，并将其添加到其应用的任何新 `GameplayEffects` 中。当服务器遇到 `WaitNetSync` 并选择 `OnlyServerWait` 时，它会等待直到接收到来自客户端的新 Scoped Prediction Key 才继续。这个 Scoped Prediction Key 和激活预测键做同样的操作——应用于 `GameplayEffects` 并复制回客户端以标记为陈旧。Scoped Prediction Key 是有效的，直到它失效，这意味着 Scoped Prediction Window 已关闭。因此，再次强调，只有原子操作，没有潜在的，可以使用新的 Scoped Prediction Key。  
   
你可以根据需要创建任意数量的 Scoped Prediction Windows。  
   
如果你想将同步点功能添加到你自己的自定义 `AbilityTasks` 中，请查看输入任务如何将 `WaitNetSync` `AbilityTask` 代码注入到其中。  
   
**注意：** 使用 `WaitNetSync` 时，这会阻止服务器的 `GameplayAbility` 继续执行，直到它从客户端听到消息。如果你担心恶意用户可能会通过黑客攻击游戏并故意延迟发送其新的 Scoped Prediction Key 来滥用这一点，Epic 推荐在此情况下可能构建一个新的 `AbilityTask` 版本，带有一个延迟，自动继续而不依赖客户端。  
   
Sample Project 在 Sprint `GameplayAbility` 中使用 `WaitNetSync` 以在每次我们应用耐力消耗时创建新的 Scoped Prediction Window，以便我们可以预测它。理想情况下，我们希望在应用消耗和冷却时拥有有效的预测键。  
   
如果你有一个预测的 `GameplayEffect` 在拥有客户端上播放了两次，那么你的预测键是陈旧的，你正在经历“重做”问题。你通常可以通过在应用 `GameplayEffect` 之前放置一个带有 `OnlyServerWait` 的 `WaitNetSync` `AbilityTask` 来创建新的 Scoped Prediction Key 来解决此问题。  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="concepts-p-spawn"></a>  
   
#### 4.10.3 预测性生成 Actor  
   
在客户端上预测性生成 `Actors` 是一个高级主题。GAS 并不提供开箱即用的功能来处理这一点（`SpawnActor` `AbilityTask` 仅在服务器上生成 `Actor`）。关键概念是同时在客户端和服务器上生成一个复制的 `Actor`。  
   
如果 `Actor` 只是装饰性的或不服务于任何游戏目的，简单的解决方案是覆盖 `Actor` 的 `IsNetRelevantFor()` 函数以限制服务器向拥有客户端复制。拥有客户端将有其本地生成的版本，服务器和其他客户端将有服务器复制的版本。  
   
```c++  
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const  
{  
    return !IsOwnedBy(ViewTarget);  
}  
```  
   
如果生成的 `Actor` 影响游戏玩法，如需要预测伤害的投射物，则需要高级逻辑，这超出了本文档的范围。查看 UnrealTournament 如何在 Epic Games 的 GitHub 上预测性生成投射物。他们有一个仅在拥有客户端上生成的虚拟投射物，与服务器复制的投射物同步。  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="concepts-p-future"></a>  
   
#### 4.10.4 GAS 预测的未来  
   
`GameplayPrediction.h` 表示将来他们可能会添加预测 `GameplayEffect` 移除和周期性 `GameplayEffects` 的功能。  
   
Epic 的 Dave Ratti [表示有兴趣](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)解决预测冷却的“延迟和解”问题，这对高延迟玩家相对于低延迟玩家不利。  
   
Epic 的新 [`Network Prediction` 插件](#concepts-p-npp) 预计将与 GAS 完全互操作，就像 `CharacterMovementComponent` 之前一样。  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="concepts-p-npp"></a>  
   
#### 4.10.5 网络预测插件  
   
Epic 最近启动了一项用新的 `Network Prediction` 插件替换 `CharacterMovementComponent` 的计划。此插件仍处于非常早期的阶段，但可在 Unreal Engine GitHub 上进行非常早期的访问。现在还无法确定它将在引擎的哪个未来版本中首次亮相为实验性测试版。  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="concepts-targeting"></a>  
   
### 4.11 目标  
   
<a name="concepts-targeting-data"></a>  
   
#### 4.11.1 目标数据  
   
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) 是一个通用的目标数据结构，旨在跨网络传递。`TargetData` 通常会保存 `AActor`/`UObject` 引用、`FHitResults` 和其他通用的位置信息/方向信息/原点信息。然而，你可以将其子类化，以便将你想要的任何内容放入其中，作为在 [`GameplayAbilities`](#concepts-ga-data) 中在客户端和服务器之间传递数据的简单手段。基础结构 `FGameplayAbilityTargetData` 并不打算直接使用，而是子类化。`GAS` 随附一些开箱即用的子类化 `FGameplayAbilityTargetData` 结构，位于 `GameplayAbilityTargetTypes.h` 中。  
   
`TargetData` 通常由 [`Target Actors`](#concepts-targeting-actors) 生成或 **手动创建**，并通过 [`EffectContext`](#concepts-ge-context) 由 [`AbilityTasks`](#concepts-at) 和 [`GameplayEffects`](#concepts-ge) 消耗。由于在 `EffectContext` 中，[`Executions`](#concepts-ge-ec)、[`MMCs`](#concepts-ge-mmc)、[`GameplayCues`](#concepts-gc) 和 [`AttributeSet`](#concepts-as) 后端的函数可以访问 `TargetData`。  
   
我们通常不直接传递 `FGameplayAbilityTargetData`，而是使用 [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html)，其中包含一个指向 `FGameplayAbilityTargetData` 的 TArray 指针。这个中间结构提供了 `TargetData` 的多态性支持。  
   
一个继承自 `FGameplayAbilityTargetData` 的例子：  
   
```c++  
USTRUCT(BlueprintType)  
struct MYGAME_API FGameplayAbilityTargetData_CustomData : public FGameplayAbilityTargetData  
{  
    GENERATED_BODY()  
public:  
  
    FGameplayAbilityTargetData_CustomData()  
    {  
    }  
  
    UPROPERTY()  
    FName CoolName = NAME_None;  
  
    UPROPERTY()  
    FPredictionKey MyCoolPredictionKey;  
  
    // 这是所有 FGameplayAbilityTargetData 子结构所需的  
    virtual UScriptStruct* GetScriptStruct() const override  
    {  
        return FGameplayAbilityTargetData_CustomData::StaticStruct();  
    }  
  
    // 这是所有 FGameplayAbilityTargetData 子结构所需的  
    bool NetSerialize(FArchive& Ar, class UPackageMap* Map, bool& bOutSuccess)  
    {  
        // 引擎已经为 FName 和 FPredictionKey 定义了 NetSerialize，感谢 Epic！  
        CoolName.NetSerialize(Ar, Map, bOutSuccess);  
        MyCoolPredictionKey.NetSerialize(Ar, Map, bOutSuccess);  
        bOutSuccess = true;  
        return true;  
    }  
}  
   
template<>  
struct TStructOpsTypeTraits<FGameplayAbilityTargetData_CustomData> : public TStructOpsTypeTraitsBase2<FGameplayAbilityTargetData_CustomData>  
{  
    enum  
    {  
        WithNetSerializer = true // 这是 FGameplayAbilityTargetDataHandle 网络序列化工作所需的  
    };  
};  
```  
   
将目标数据添加到句柄的示例：  
   
```c++  
UFUNCTION(BlueprintPure)  
FGameplayAbilityTargetDataHandle MakeTargetDataFromCustomName(const FName CustomName)  
{  
    // 创建我们的目标数据类型，  
    // 句柄会在句柄销毁时自动清理并删除此数据，  
    // 如果你不将其添加到句柄中，则要小心，因为这涉及内存管理和内存泄漏，因此最好在某个帧中始终将其添加到句柄中！  
    FGameplayAbilityTargetData_CustomData* MyCustomData = new FGameplayAbilityTargetData_CustomData();  
    // 设置结构的信息以使用输入的名称以及我们可能希望进行的其他更改  
    MyCustomData->CoolName = CustomName;  
  
    // 为 Blueprint 使用制作我们的句柄包装器  
    FGameplayAbilityTargetDataHandle Handle;  
    // 将目标数据添加到我们的句柄中  
    Handle.Add(MyCustomData);  
    // 输出我们的句柄到 Blueprint  
    return Handle;  
}  
```  
   
获取值时需要进行类型安全检查，因为从句柄的目标数据中获取值的唯一方法是使用泛型 C/C++ 转换，这是*非*类型安全的，可能导致对象切片和崩溃。对于类型检查，有多种方法（实际上你想怎么做就怎么做），两种常见的方法是：  
   
- 游戏标签：你可以使用一个子类层次结构，当你知道任何时候某个代码结构的功能发生时，你可以为基础父类型进行转换并获取其游戏标签，然后与这些标签进行比较以进行继承类的转换。  
- 脚本结构和静态结构：你可以直接进行类比较（这可能涉及很多 IF 语句或制作一些模板函数），下面是一个使用这些函数进行类型检查的示例：你可以从任何 `FGameplayAbilityTargetData` 获取脚本结构（这是它是 `USTRUCT` 的一个好处，并要求任何继承类在 `GetScriptStruct` 中指定结构类型），并比较它是否是你正在寻找的类型。下面是使用这些函数进行类型检查的示例：  
   
```c++  
UFUNCTION(BlueprintPure)  
FName GetCoolNameFromTargetData(const FGameplayAbilityTargetDataHandle& Handle, const int Index)  
{  
    // 注意，这里有两个版本的 '::Get(int32 Index)' 函数；  
    // 1）返回 'const FGameplayAbilityTargetData*' 的 const 版本，适用于读取目标数据值  
    // 2）返回 'FGameplayAbilityTargetData*' 的非 const 版本，适用于修改目标数据值  
    FGameplayAbilityTargetData* Data = Handle.Get(Index); // 这会为你有效检查索引  
  
    // 检查我们是否有东西可用，null 数据意味着没有要转换的内容  
    if (Data == nullptr)  
    {  
        return NAME_None;  
    }  
    // 这基本上是类型检查过程，static_cast 没有类型安全性，这就是为什么我们要进行此检查。  
    // 如果我们不这样做，那么它将切片结构对象，因此我们无法确保它是该类型。  
    if (Data->GetScriptStruct() == FGameplayAbilityTargetData_CustomData::StaticStruct())  
    {  
        // 这是你可以进行转换的地方，因为我们知道它已经是正确的类型  
        FGameplayAbilityTargetData_CustomData* CustomData = static_cast<FGameplayAbilityTargetData_CustomData*>(Data);  
        return CustomData->CoolName;  
    }  
    return NAME_None;  
}  
```  
   
**[⬆ 返回顶部](#table-of-contents)**  


<a name="concepts-targeting-actors"></a>
#### 4.11.2 目标Actors (Target Actors)
`GameplayAbilities` 会通过 `WaitTargetData` 的 `AbilityTask` 创建 [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html)，以便可视化并从世界中捕捉目标信息。`TargetActors` 可以选择使用 [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) 来显示当前的目标。在确认后，目标信息会作为 [`TargetData`](#concepts-targeting-data) 返回，随后可以传递给 `GameplayEffects`。

`TargetActors` 基于 `AActor`，因此可以拥有任何类型的可视组件来表示**在哪里**以及**如何**进行目标锁定，例如静态网格或贴花。静态网格可用于可视化角色将要建造的物体的位置。贴花可用于在地面上显示效果范围。例如，示例项目使用 [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) 和地面的贴花来表示流星技能的伤害范围。它们也不需要显示任何内容。例如，对于瞬时目标射击的武器（如 [GASShooter](https://github.com/tranek/GASShooter) 中的例子），显示任何内容都是没有意义的。

它们使用基本的追踪或碰撞重叠来捕捉目标信息，并根据 `TargetActor` 实现将结果转换为 `FHitResults` 或 `AActor` 数组，然后转换为 `TargetData`。`WaitTargetData` 的 `AbilityTask` 通过其 `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` 参数来确定何时确认目标。当不使用 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant}` 时，`TargetActor` 通常在 `Tick()` 中执行追踪/重叠，并根据其实现更新位置到 `FHitResult`。虽然在 `Tick()` 中执行追踪/重叠非常响应客户端，但一般不会太糟糕，因为它不会复制并且通常一次只有一个（尽管可以有多个）`TargetActor` 在运行。只需注意，它使用 `Tick()`，而一些复杂的 `TargetActors` 可能会做很多事情，比如在 GASShooter 中火箭发射器的副技能。尽管在 `Tick()` 中进行追踪响应速度很快，但如果性能影响太大，您可能会考虑降低 `TargetActor` 的 tick 率。对于 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant}`，`TargetActor` 会立即生成、产生 `TargetData` 并销毁。`Tick()` 永远不会被调用。

| `EGameplayTargetingConfirmation::Type` | 确认目标的时机                                                                                                                                                                                                                                                                                                                    |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | 目标锁定立即发生，无需特殊逻辑或用户输入决定何时“发射”。                                                                                                                                                                                                                                                                  |
| `UserConfirmed`                        | 当用户确认目标时（[技能绑定到 `Confirm` 输入](#concepts-ga-input) 或调用 `UAbilitySystemComponent::TargetConfirm()`），目标锁定发生。`TargetActor` 也会响应绑定的 `Cancel` 输入或调用 `UAbilitySystemComponent::TargetCancel()` 以取消目标锁定。                                            |
| `Custom`                               | `GameplayTargeting` 能力负责通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()` 来决定何时目标数据准备好。`TargetActor` 也会响应 `UGameplayAbility::CancelTaskByInstanceName()` 以取消目标锁定。                                                                                                       |
| `CustomMulti`                          | `GameplayTargeting` 能力负责通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()` 来决定何时目标数据准备好。`TargetActor` 也会响应 `UGameplayAbility::CancelTaskByInstanceName()` 以取消目标锁定。数据生成后不应结束 `AbilityTask`。                                                                     |

并不是每个 `EGameplayTargetingConfirmation::Type` 都支持每个 `TargetActor`。例如，`AGameplayAbilityTargetActor_GroundTrace` 不支持 `Instant` 确认。

`WaitTargetData` 的 `AbilityTask` 接受 `AGameplayAbilityTargetActor` 类作为参数，并且每次激活 `AbilityTask` 时都会生成一个实例，`AbilityTask` 结束时会销毁 `TargetActor`。`WaitTargetDataUsingActor` 的 `AbilityTask` 接受已经生成的 `TargetActor`，但在 `AbilityTask` 结束时仍会销毁它。无论是哪种 `AbilityTask`，它们都不是特别高效，因为每次使用时都需要生成一个新的 `TargetActor` 或者要求使用一个新的 `TargetActor`。它们适用于原型开发，但在生产环境中，如果您有不断生成 `TargetData` 的情况（例如自动步枪的情况），您可能需要优化它。GASShooter 中有一个 `AGameplayAbilityTargetActor` 的自定义子类和一个从头编写的 [`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) `AbilityTask`，可以让您在不销毁的情况下重复使用 `TargetActor`。

默认情况下，`TargetActors` 不会被复制；然而，如果您的游戏需要向其他玩家显示本地玩家的目标，可以使其复制。它们确实包括与服务器通过 `WaitTargetData` 的 `AbilityTask` 进行通信的默认功能。如果 `TargetActor` 的 `ShouldProduceTargetDataOnServer` 属性设置为 `false`，那么客户端会在确认时通过 `CallServerSetReplicatedTargetData()` 将 `TargetData` 通过 RPC 发送给服务器，方法是在 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` 中。如果 `ShouldProduceTargetDataOnServer` 为 `true`，客户端会通过 `EAbilityGenericReplicatedEvent::GenericConfirm` 向服务器发送一个通用的确认事件 RPC，在收到该 RPC 后，服务器将执行追踪或重叠检查并生成服务器上的数据。如果客户端取消了目标锁定，它会通过 `EAbilityGenericReplicatedEvent::GenericCancel` 发送一个通用取消事件的 RPC。在 `TargetActor` 和 `WaitTargetData` `AbilityTask` 上都有许多代理。`TargetActor` 响应输入以生成并广播目标数据准备、确认或取消代理。`WaitTargetData` 监听 `TargetActor` 的目标数据准备、确认和取消代理，并将这些信息反馈给 `GameplayAbility` 和服务器。如果您将 `TargetData` 发送到服务器，您可能需要在服务器上进行验证，以确保 `TargetData` 合理，以防作弊。直接在服务器上生成 `TargetData` 可以完全避免这个问题，但可能会导致拥有客户端的预测错误。

根据您使用的 `AGameplayAbilityTargetActor` 的具体子类，`WaitTargetData` `AbilityTask` 节点上会暴露不同的 `ExposeOnSpawn` 参数。一些常见的参数包括：

| 常见的 `TargetActor` 参数        | 定义                                                                                                                                                                                                                                                                                                |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Debug                           | 如果为 `true`，每当 `TargetActor` 执行追踪时（在非发布版本中），会绘制调试追踪/重叠信息。记住，非 `Instant` 的 `TargetActors` 会在 `Tick()` 中执行追踪，因此这些调试绘制调用也会在 `Tick()` 中发生。                                                                                                   |
| 过滤（Filter）                          | [可选] 用于在追踪/重叠时过滤掉（移除）目标的特殊结构体。常见用法包括过滤掉玩家的 `Pawn`，或者要求目标是特定类。有关更多高级用法，请参见 [目标数据过滤器](#concepts-target-data-filters)。                                                                                                                                     |
| Reticle Class                   | [可选] `TargetActor` 将生成的 `AGameplayAbilityWorldReticle` 的子类。                                                                                                                                                                                                                                      |
| Reticle Parameters              | [可选] 配置您的 Reticles。请参见 [Reticles](#concepts-targeting-reticles)。                                                                                                                                                                                                                                     |
| 起始位置（Start Location）                  | 一个特殊结构体，用于定义追踪的起始位置。通常，这将是玩家的视点、武器枪口或 `Pawn` 的位置。                                                                                                                                                                               |


对于默认的`TargetActor`类，`Actors`只有在直接处于跟踪/重叠中时才是有效目标。如果它们离开跟踪/重叠（它们移动或您移开视线），它们就不再有效。如果您希望`TargetActor`记住最后一个有效目标，则需要将此功能添加到自定义`TargetActor`类中。我将这些称为持久目标，因为它们将一直存在，直到`TargetActor`收到确认或取消，`TargetActor`在其跟踪/重叠中找到新的有效目标，或者目标不再有效（被破坏）。`GASShooter`使用持久目标作为其火箭发射器辅助能力的制导火箭瞄准。

**[⬆ 返回顶部](#table-of-contents)**


<a name="concepts-target-data-filters"></a>
#### 4.11.3 目标数据过滤器
使用 `Make GameplayTargetDataFilter` 和 `Make Filter Handle` 节点，您可以过滤玩家的 `Pawn` 或仅选择特定的类。如果需要更高级的过滤，您可以继承 `FGameplayTargetDataFilter` 并重写 `FilterPassesForActor` 函数。

```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** 如果演员通过过滤并将成为目标，则返回true */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

但是，这不会直接作用于 `Wait Target Data` 节点，因为它需要一个 `FGameplayTargetDataFilterHandle`。必须创建一个新的自定义 `Make Filter Handle` 来接受该子类：

```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 游戏玩法能力世界准星
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html)（`Reticles`）可视化**您**在使用非 `Instant` 确认的 [`TargetActors`](#concepts-targeting-actors) 时正在锁定的目标。`TargetActors` 负责所有 `Reticles` 的生成和销毁生命周期。`Reticles` 是 `AActor` 类型，因此它们可以使用任何类型的视觉组件进行表示。在 [GASShooter](https://github.com/tranek/GASShooter) 中常见的实现是使用 `WidgetComponent` 来显示一个 UMG Widget，在屏幕空间中（始终面朝玩家的摄像机）。`Reticles` 不知道它们所在的 `AActor`，但您可以在自定义 `TargetActor` 上继承该功能。`TargetActors` 通常会在每个 `Tick()` 更新 `Reticle` 的位置到目标的位置。

GASShooter 使用 `Reticles` 来显示火箭发射器的次要能力的锁定目标的制导火箭。敌人上的红色指示器是 `Reticle`。类似的白色图像是火箭发射器的准星。
![Reticles in GASShooter](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles` 配备了一些 `BlueprintImplementableEvents`，供设计师使用（这些事件旨在通过蓝图开发）：

```c++
/** 每当 bIsTargetValid 的值发生变化时调用。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** 每当 bIsTargetAnActor 的值发生变化时调用。 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles` 可以选择使用 [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) 来进行配置，`TargetActor` 提供此配置。默认的结构体只提供一个变量 `FVector AOEScale`。尽管您可以在技术上继承这个结构体，但 `TargetActor` 仅接受基本结构体。虽然这限制了默认 `TargetActors` 的功能，但如果您创建自己的自定义 `TargetActor`，您可以提供自定义的准星参数结构体，并在生成时手动传递给您的 `AGameplayAbilityWorldReticles` 子类。

`Reticles` 默认情况下不会进行复制，但如果您的游戏需要向其他玩家显示本地玩家正在锁定的目标，可以让其进行复制。

默认的 `TargetActors` 只会在当前有效的目标上显示 `Reticle`。例如，如果您使用 `AGameplayAbilityTargetActor_SingleLineTrace` 来跟踪目标，则只有当敌人正好位于追踪路径中时，`Reticle` 才会显示。如果您转移视线，敌人就不再是有效目标，`Reticle` 会消失。如果您希望 `Reticle` 始终停留在最后一个有效目标上，您需要自定义 `TargetActor` 以记住最后一个有效目标，并保持 `Reticle` 在它们身上。我称之为“持久目标”，因为它们会持续存在，直到 `TargetActor` 收到确认或取消，或者 `TargetActor` 在其追踪/重叠中找到新的有效目标，或者目标不再有效（被销毁）。GASShooter 使用持久目标来锁定火箭发射器的次要能力制导火箭的目标。

**[⬆ 返回顶部](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 游戏玩法效果容器目标选择
[`GameplayEffectContainers`](#concepts-ge-containers) 提供了一种可选的高效方式来生成 [`TargetData`](#concepts-targeting-data)。当客户端和服务器应用 `EffectContainer` 时，目标选择会立即发生。它比 [`TargetActors`](#concepts-targeting-actors) 更高效，因为它在目标对象的 CDO 上运行（无需生成和销毁 `Actors`），但它没有玩家输入，立即发生无需确认，不能取消，也不能从客户端向服务器发送数据（它在客户端和服务器上都会生成数据）。它非常适合用于即时的跟踪和碰撞重叠。Epic 的 [Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) 提供了使用其容器的两种目标选择示例——目标为能力拥有者并从事件中提取 `TargetData`。它还通过蓝图实现了一个示例，能够在从玩家的偏移量（由子蓝图类设置）执行即时球形追踪时生成目标数据。您可以在 C++ 或蓝图中继承 `URPGTargetType` 来创建自己的目标类型。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae"></a>
## 5. 常见实现的能力和效果

<a name="cae-stun"></a>
### 5.1 眩晕(Stun)
通常对于眩晕，我们希望取消 `Character` 所有活动的 `GameplayAbilities`，阻止新的 `GameplayAbility` 激活，并在眩晕持续期间防止移动。示例项目中的 Meteor `GameplayAbility` 会对击中的目标应用眩晕效果。

为了取消目标的活动 `GameplayAbilities`，我们在添加眩晕 [`GameplayTag`](#concepts-gt-change) 时调用 `AbilitySystemComponent->CancelAbilities()`。

为了防止在眩晕期间激活新的 `GameplayAbilities`，我们将眩晕 `GameplayTag` 添加到它们的 [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags) 中。

为了防止在眩晕期间移动，我们覆盖 `CharacterMovementComponent` 的 `GetMaxSpeed()` 函数，使其在拥有眩晕 `GameplayTag` 时返回 0。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 冲刺(Sprint)
示例项目提供了一个冲刺示例——在按住 `Left Shift` 键时加速跑步。

加速的移动通过 `CharacterMovementComponent` 预测处理，并通过网络将标志发送到服务器。详细信息请参见 `GDCharacterMovementComponent.h/cpp`。

`GA` 处理响应 `Left Shift` 输入，告诉 `CMC` 开始和停止冲刺，并在按住 `Left Shift` 键时预测性地消耗体力。详细信息请参见 `GA_Sprint_BP`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 瞄准镜头(Aim Down Sights)
示例项目与冲刺的处理方式完全相同，但减少了移动速度而不是增加速度。

请参见 `GDCharacterMovementComponent.h/cpp` 中的详细信息，以预测性地减少移动速度。

请参见 `GA_AimDownSight_BP` 以了解如何处理输入。瞄准镜头不会消耗体力。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 吸血(Lifesteal)
我在伤害的 [`ExecutionCalculation`](#concepts-ge-ec) 中处理吸血效果。`GameplayEffect` 会有一个类似 `Effect.CanLifesteal` 的 `GameplayTag`。`ExecutionCalculation` 会检查 `GameplayEffectSpec` 是否包含该 `Effect.CanLifesteal` `GameplayTag`。如果存在该 `GameplayTag`，`ExecutionCalculation` 会创建一个动态的 `Instant` 类型的 `GameplayEffect`，其修改值为吸取的生命值，并将其应用回 `Source` 的 `ASC`。

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 在客户端和服务器上生成随机数
有时，您需要在 `GameplayAbility` 中生成“随机”数，例如用于子弹后坐力或扩散。客户端和服务器都希望生成相同的随机数。为此，我们必须确保在 `GameplayAbility` 激活时，`random seed` 设置为相同的值。如果客户端误预测了激活并且其随机数序列与服务器的不同步，则每次激活 `GameplayAbility` 时都需要重新设置 `random seed`。

| 设置方法                                                                  | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 使用激活预测键                                                            | `GameplayAbility` 激活预测键是一个 int16，它保证在客户端和服务器中同步并在 `Activation()` 中可用。您可以将其设置为客户端和服务器的 `random seed`。这种方法的缺点是每次游戏启动时，预测键都会从零开始，并在生成键时一致地递增。这意味着每场比赛将具有完全相同的随机数序列，这可能不足够随机，具体取决于您的需求。                                                                                                               |
| 在激活 `GameplayAbility` 时通过事件负载发送种子                          | 通过事件激活 `GameplayAbility`，并将客户端生成的随机种子发送给服务器，作为事件负载进行复制。这允许更大的随机性，但客户端可能会轻松地修改游戏，使其每次发送相同的种子值。此外，通过事件激活 `GameplayAbilities` 将阻止它们通过输入绑定激活。                                                                                                                                                                                              |

如果您的随机偏差较小，大多数玩家不会注意到每场比赛中的序列是相同的，因此使用激活预测键作为 `random seed` 应该适合您。如果您做的是更复杂的任务，并且需要防止黑客篡改，也许使用 `Server Initiated` 的 `GameplayAbility` 更为合适，在这种情况下，服务器可以生成预测键或生成随机种子并通过事件负载发送。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 暴击(Critical Hits)
我在伤害的 [`ExecutionCalculation`](#concepts-ge-ec) 中处理暴击。`GameplayEffect` 会有一个类似 `Effect.CanCrit` 的 `GameplayTag`。`ExecutionCalculation` 会检查 `GameplayEffectSpec` 是否包含该 `Effect.CanCrit` `GameplayTag`。如果存在该 `GameplayTag`，`ExecutionCalculation` 会根据暴击几率（从 `Source` 捕获的 `Attribute`）生成一个随机数，并在成功时增加暴击伤害（这也是从 `Source` 捕获的 `Attribute`）。由于我不预测伤害，所以不需要担心客户端和服务器之间的随机数同步问题，因为 `ExecutionCalculation` 只会在服务器上运行。如果您尝试通过 `MMC` 进行预测性伤害计算，则必须从 `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance` 中获取对 `random seed` 的引用。

查看 [GASShooter](https://github.com/tranek/GASShooter) 如何处理爆头。它是相同的概念，除了它不依赖于随机数来判断几率，而是检查 `FHitResult` 的骨骼名称。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 非叠加的游戏效果，但只有最大幅度才能真正影响目标
《Paragon》中的减速效果不会叠加。每次减速效果都会被应用并跟踪其持续时间，但只有最大幅度的减速效果会真正影响`Character`。GAS默认提供了对这种情况的支持，使用`AggregatorEvaluateMetaData`。有关详细信息和实现，请参见[`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 在游戏暂停时生成目标数据
如果您需要在暂停游戏时等待从玩家的`WaitTargetData` `AbilityTask`生成[`TargetData`](#concepts-targeting-data)，建议不要暂停游戏，而是使用`slomo 0`。

**[⬆ 返回顶部](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 一键交互系统
[GASShooter](https://github.com/tranek/GASShooter)实现了一键交互系统，玩家可以按下或长按'E'键与可交互的物体互动，比如复活玩家、打开武器箱、开关滑动门等。

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging"></a>
## 6. 调试GAS
在调试与GAS相关的问题时，您可能会想知道以下内容：
> * "我的属性值是多少？"
> * "我有哪些游戏玩法标签？"
> * "我当前有哪些游戏玩法效果？"
> * "我被授予了哪些能力，哪些正在运行，哪些被阻止激活？"

GAS提供了两种在运行时回答这些问题的技术——[`showdebug abilitysystem`](#debugging-sd)和[`GameplayDebugger`](#debugging-gd)中的钩子。

**提示：** Unreal Engine喜欢优化C++代码，这使得调试某些函数变得困难。当您深入追踪代码时，偶尔会遇到这种情况。如果将Visual Studio解决方案配置设置为`DebugGame Editor`仍然无法跟踪代码或检查变量，您可以通过在优化函数周围包裹`UE_DISABLE_OPTIMIZATION`和`UE_ENABLE_OPTIMIZATION`宏，或使用CoreMiscDefines.h中定义的发布版本进行禁用所有优化。在没有重新编译插件源代码的情况下，无法对插件代码使用此方法。根据它们的功能和位置，这可能对内联函数不适用。调试完成后，请务必删除这些宏！

```c++
UE_DISABLE_OPTIMIZATION
void MyClass::MyFunction(int32 MyIntParameter)
{
	// 我的代码
}
UE_ENABLE_OPTIMIZATION
```

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
在游戏内控制台输入`showdebug abilitysystem`。此功能分为三个“页面”。所有三个页面都会显示您当前拥有的`GameplayTags`。输入`AbilitySystem.Debug.NextCategory`到控制台可以在页面之间切换。

第一页显示所有`Attributes`的`CurrentValue`：
![showdebug abilitysystem第一页](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

第二页显示您身上的所有`Duration`和`Infinite`类型的`GameplayEffects`，它们的堆叠数、所附加的`GameplayTags`以及给出的`Modifiers`。
![showdebug abilitysystem第二页](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

第三页显示已授予您的所有`GameplayAbilities`，它们是否正在运行，是否被阻止激活，以及当前正在运行的`AbilityTasks`的状态。
![showdebug abilitysystem第三页](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

要在目标之间切换（目标通过演员周围的绿色矩形体积表示），使用`PageUp`键或`NextDebugTarget`控制台命令切换到下一个目标，使用`PageDown`键或`PreviousDebugTarget`控制台命令切换到上一个目标。

**注意：** 为了让能力系统信息基于当前选择的调试演员更新，您需要在`DefaultGame.ini`中将`AbilitySystemGlobals`设置为`bUseDebugTargetFromHud=true`：
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
bUseDebugTargetFromHud=true
```

**注意：** 要使`showdebug abilitysystem`工作，游戏模式中必须选择实际的HUD类。否则会返回“未知命令”。

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger
`GAS`为`Gameplay Debugger`添加了功能。按下撇号（`'`）键访问`Gameplay Debugger`。通过按数字小键盘上的`3`键启用`Abilities`类别。根据您拥有的插件，类别可能会有所不同。如果您的键盘没有小键盘（如笔记本电脑），您可以在项目设置中更改键绑定。

当您想查看**其他**`Character`的`GameplayTags`、`GameplayEffects`和`GameplayAbilities`时，使用`Gameplay Debugger`。不幸的是，它不会显示目标的`Attributes`的`CurrentValue`。它将锁定在屏幕中心的`Character`上。您可以通过在编辑器中的世界大纲中选择目标，或者通过再次按撇号（'）键来切换目标。当前检查的`Character`上方会有一个最大的红色圆圈。

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS日志
GAS源代码包含大量的日志语句，这些语句在不同的详细程度下生成。您最常见的是`ABILITY_LOG()`语句。默认的详细级别是`Display`，任何更高的级别默认不会显示在控制台中。

要更改日志类别的详细级别，请在控制台中输入：

```
log [category] [verbosity]
```

例如，要打开`ABILITY_LOG()`语句，您可以在控制台输入：
```
log LogAbilitySystem VeryVerbose
```

要重置为默认设置，输入：
```
log LogAbilitySystem Display
```

要显示所有日志类别，输入：
```
log list
```

与GAS相关的显著日志类别：

| 日志类别                 | 默认详细级别         |
| ------------------------ | -------------------- |
| LogAbilitySystem         | Display              |
| LogAbilitySystemComponent| Log                  |
| LogGameplayCueDetails    | Log                  |
| LogGameplayCueTranslator | Display              |
| LogGameplayEffectDetails | Log                  |
| LogGameplayEffects       | Display              |
| LogGameplayTags          | Log                  |
| LogGameplayTasks         | Log                  |
| VLogAbilitySystem        | Display              |

有关更多信息，请参见[Wiki上的日志记录](https://unrealcommunity.wiki/logging-lgpidy6i)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="optimizations"></a>
## 7. 优化

<a name="optimizations-abilitybatching"></a>
### 7.1 能力批处理
[`GameplayAbilities`](#concepts-ga)在激活时，通常会将`TargetData`发送到服务器，并且都在同一帧结束，可以[批处理为一个RPC](#concepts-ga-batching)，以将两个到三个RPC合并为一个RPC。这类能力通常用于命中扫描枪。

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 游戏玩法提示批处理
如果您同时发送许多[`GameplayCues`](#concepts-gc)，考虑[将它们批处理成一个RPC](#concepts-gc-batching)。目标是减少RPC的数量（因为`GameplayCues`是不可预测的NetMulticast），并尽可能少地发送数据。

<a name="optimizations-ascreplicationmode"></a>
### 7.3 AbilitySystemComponent的复制模式
默认情况下，[`ASC`](#concepts-asc)处于[`Full Replication Mode`](#concepts-asc-rm)模式。这将把所有[`GameplayEffects`](#concepts-ge)复制到每个客户端（对于单人游戏来说没问题）。在多人游戏中，将玩家拥有的`ASC`设置为`Mixed Replication Mode`，将AI控制的角色设置为`Minimal Replication Mode`。这将只将应用在玩家角色上的`GE`复制给该角色的拥有者，而AI控制的角色上的`GE`将永远不会复制到客户端。`GameplayTags`仍然会复制，`GameplayCues`仍然会通过不可预测的NetMulticast复制到所有客户端，无论`Replication Mode`如何。这将减少在所有客户端不需要看到的`GE`的网络数据传输。

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 属性代理复制
在像Fortnite Battle Royale（FNBR）这样的多人游戏中，会有很多[`ASC`](#concepts-asc)驻留在始终相关的`PlayerStates`上，复制大量的[`Attributes`](#concepts-a)。为了优化这个瓶颈，FNBR禁用了在**模拟玩家控制的代理**中的`ASC`及其[`AttributeSets`](#concepts-as)的复制。在自动代理和AI控制的`Pawns`中，`ASC`仍会完全复制，具体取决于它们的[`Replication Mode`](#concepts-asc-rm)。FNBR不再将`Attributes`复制到始终相关的`PlayerStates`上的`ASC`中，而是使用玩家`Pawn`上的代理结构进行复制。当服务器上的`ASC`中的`Attributes`发生变化时，代理结构中的相应`Attributes`也会发生变化。客户端从代理结构接收已复制的`Attributes`并将变化推送回本地`ASC`。这种做法利用了`Pawn`的相关性和`NetUpdateFrequency`，减少了网络数据量。这一优化不适用于AI控制的`Pawns`，因为它们已经使用`Pawn`的相关性。

> 我不确定这是否仍然必要，尤其是在之后进行的其他服务器端优化（如复制图等）之后，这也不是最可维护的模式。

*Dave Ratti, Epic在[社区问题#3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)的回答*

<a name="optimizations-asclazyloading"></a>
### 7.5 ASC延迟加载
Fortnite Battle Royale（FNBR）世界中有大量可损坏的`AActors`（树木、建筑等），每个都有一个[`ASC`](#concepts-asc)。这会增加内存开销。FNBR通过仅在需要时加载`ASC`（当它们首次受到玩家的伤害时）来优化这一点。由于某些`AActors`可能在比赛中从未受到伤害，因此可以减少总体内存使用量。

**[⬆ 返回顶部](#table-of-contents)**

<a name="qol"></a>
## 8. 生命周期质量建议（Quality of Life Suggestions）

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 游戏玩法效果容器（GameplayEffectContainers）
[GameplayEffectContainers](#concepts-ge-containers)将[`GameplayEffectSpecs`](#concepts-ge-spec)、[`TargetData`](#concepts-targeting-data)、[简单目标](#concepts-targeting-containers)和相关功能组合成易于使用的结构。这些非常适合将`GameplayEffectSpecs`传递给从能力生成的投射物，然后在碰撞时稍后应用它们。

<a name="qol-asynctasksascdelegates"></a>
### 8.2 将蓝图 · 绑定到 · 委托
为了提高设计师友好的迭代速度，特别是在设计UMG小部件UI时，可以创建Blueprint异步任务（在C++中），直接绑定到`ASC`上的常见变化委托。唯一的注意事项是，必须手动销毁这些异步任务（如销毁小部件时），否则它们将永远驻留在内存中。示例项目包括三个Blueprint异步任务。

监听`Attribute`的变化：

![监听属性变化BP节点](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

监听冷却时间变化：

![监听冷却时间变化BP节点](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

监听`GE`堆叠变化：

![监听游戏玩法效果堆叠变化BP节点](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. 故障排除

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
您需要[在客户端初始化`ASC`](#concepts-asc-setup)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache`错误
您需要调用[`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata)。

**[⬆ 返回顶部](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 动画蒙太奇未复制到客户端
确保在您的[GameplayAbilities](#concepts-ga)中使用`PlayMontageAndWait` Blueprint节点，而不是`PlayMontage`。此[AbilityTask](#concepts-at)会通过`ASC`自动复制蒙太奇，而`PlayMontage`节点则不会。

**[⬆ 返回顶部](#table-of-contents)**


<a name="troubleshooting-duplicatingblueprintactors"></a>  
   
### 9.4 复制蓝图Acotr将AttributeSets设置为 nullptr  
   
[Unreal Engine 的一个 bug](https://issues.unrealengine.com/issue/UE-81109) 会将从现有蓝图`Acotr`类复制的蓝图演员类中的 `AttributeSet` 指针设置为 `nullptr`。对此有一些解决方法。我成功地避免在我的类中创建定制的 `AttributeSet` 指针（在 .h 中没有指针，在构造函数中不调用 `CreateDefaultSubobject`），而是直接在 `PostInitializeComponents()` 中将 `AttributeSets` 添加到 `ASC` 中（在示例项目中未显示）。复制的 `AttributeSets` 仍然存在于 `ASC` 的 `SpawnedAttributes` 数组中。它的代码如下所示：  
   
```c++  
void AGDPlayerState::PostInitializeComponents() {  
    Super::PostInitializeComponents();  
    if (AbilitySystemComponent) {  
        AbilitySystemComponent->AddSet<UGDAttributeSetBase>();  
        // ... 你可能还有的其他 AttributeSets  
    }  
}  
```  
   
在这种情况下，您将使用 `ASC` 上的函数来读取和设置 `AttributeSet` 中的值，而不是[调用由宏创建的 `AttributeSet` 上的函数](#concepts-as-attributes)。  
   
```c++  
/** 返回属性的当前（最终）值 */  
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;  
   
/** 设置属性的基础值。现有的活动修改器不会被清除，并将作用于新的基础值。 */  
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);  
```  
   
因此，`GetHealth()` 的代码可能如下所示：  
   
```c++  
float AGDPlayerState::GetHealth() const {  
    if (AbilitySystemComponent) {  
        return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());  
    }  
    return 0.0f;  
}  
```  
   
设置（初始化）健康 `Attribute` 的代码可能如下所示：  
   
```c++  
const float NewHealth = 100.0f;  
if (AbilitySystemComponent) {  
    AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);  
}  
```  
   
提醒一下，`ASC` 至多只期望每个 `AttributeSet` 类有一个 `AttributeSet` 对象。  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="troubleshooting-unresolvedexternalsymbolmarkpropertydirty"></a>  
   
### 9.5 未解决的外部符号 UEPushModelPrivate::MarkPropertyDirty(int,int)  
   
如果您遇到如下编译器错误：  
   
```error  
LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)  
```  
   
这是因为尝试在 `FFastArraySerializer` 上调用 `MarkItemDirty()` 时发生的。我在更新 `ActiveGameplayEffect` 时遇到了这个问题，例如在更新冷却时间时。  
   
```c++  
ActiveGameplayEffects.MarkItemDirty(*AGE);  
```  
   
发生的问题是 `WITH_PUSH_MODEL` 在多个地方被定义。`PushModelMacros.h` 将其定义为 0，而在多个地方将其定义为 1。`PushModel.h` 看到的是 1，而 `PushModel.cpp` 看到的是 0。  
   
解决方案是在项目的 `Build.cs` 中将 `NetCore` 添加到 `PublicDependencyModuleNames`。  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="troubleshooting-enumnamesarenowpathnames"></a>  
   
### 9.6 枚举名称现在由路径名表示  
   
如果您收到类似的编译器警告：  
   
```warning  
C4996: 'FGameplayAbilityInputBinds::FGameplayAbilityInputBinds': Enum names are now represented by path names. Please use a version of FGameplayAbilityInputBinds constructor that accepts FTopLevelAssetPath. Please update your code to the new API before upgrading to the next release, otherwise your project will no longer compile.  
```  
   
UE 5.1 弃用了在 `BindAbilityActivationToInputComponent()` 的构造函数中使用 `FString`。相反，我们必须传入 `FTopLevelAssetPath`。  
   
旧的、被弃用的方式：  
   
```c++  
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),	FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));  
```  
   
新的方式：  
   
```c++  
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));  
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));  
```  
   
更多信息请参见 `Engine\Source\Runtime\CoreUObject\Public\UObject\TopLevelAssetPath.h`。  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="acronyms"></a>  
   
## 10. 常见的 GAS 缩略词  
   
| 名称                                                                                                   | 缩略词              |  
|------------------------------------------------------------------------------------------------------- | ------------------- |  
| AbilitySystemComponent                                                                                 | ASC                 |  
| AbilityTask                                                                                            | AT                  |  
| [Epic 的 Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)       | ARPG, ARPG Sample   |  
| CharacterMovementComponent                                                                             | CMC                 |  
| GameplayAbility                                                                                        | GA                  |  
| GameplayAbilitySystem                                                                                  | GAS                 |  
| GameplayCue                                                                                            | GC                  |  
| GameplayEffect                                                                                         | GE                  |  
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, Execution |  
| GameplayTag                                                                                            | Tag, GT             |  
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |  
   
**[⬆ 返回顶部](#table-of-contents)**  
   
<a name="resources"></a>  
   
## 11. 其他资源  
   
* [官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)  
* 源代码！   
  * 特别是 `GameplayPrediction.h`  
* [Epic 的 Lyra 示例项目](https://unrealengine.com/marketplace/en-US/learn/lyra)  
* [Epic 的 Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)  
* [Unreal Slackers Discord](https://unrealslackers.org/) 有一个专门用于 GAS 的文本频道 `#gameplay-ability-system`  
  * 查看置顶消息  
* [Dan 'Pan' 的 GitHub 资源库](https://github.com/Pantong51/GASContent)  
* [SabreDartStudios 的 YouTube 视频](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)  
   
<a name="resources-daveratti"></a>  
   
### 11.1 与 Epic 游戏公司的 Dave Ratti 的问答  

<a name="resources-daveratti-community1"></a>
#### 11.1.1 Community Questions 1
[Dave Ratti 对 Unreal Slackers Discord Server 社区关于 GAS 的问题的回答](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. **如何在不依赖`GameplayAbilities`的情况下创建按需的预测窗口？例如，当一个“发射即忘”的投射物击中敌人时，如何在本地预测伤害`GameplayEffect`？**

> `PredictionKey`系统并不是为了处理这种情况而设计的。从根本上说，这个系统通过客户端发起预测操作，使用一个键将其告知服务器，然后客户端和服务器执行相同的操作，并将预测副作用与给定的预测键关联。例如，“我正在预测性地激活一个能力”或“我已经生成了目标数据，并准备在WaitTargetData任务之后预测性地运行能力图的下一部分”。
>
> 在这种模式下，预测键会“反弹”回服务器，并通过UAbilitySystemComponent::ReplicatedPredictionKeyMap（已复制属性）返回给客户端。一旦键从服务器复制回客户端，客户端就能够撤销所有本地预测的副作用（如GameplayCues和GameplayEffects）：复制的版本*会在那里*，如果没有，则说明是错误的预测。在此，准确知道何时撤销预测副作用至关重要：如果过早撤销，将看到缺失；如果过晚撤销，则会出现“双重效果”。（注意，这里指的是有状态的预测，例如持续时间基础的GameplayEffect的循环GameplayCue，“Burst”GameplayCues和即时GameplayEffects从不被“撤销”或回滚。如果与它们相关联的预测键存在，则客户端会跳过这些效果）。
>
> 为了进一步强调这一点：至关重要的是，预测性操作是服务器在客户端告知它时才会执行的，而不是服务器自己做的。因此，像“按需创建键并告诉服务器，我可以运行某些东西”这样的做法是行不通的，除非“某些东西”是服务器只在客户端告知后才会执行的。
>
> 回到原始问题：类似于“发射即忘”的投射物。Paragon和Fortnite都有使用GameplayCues的投射物演员类。然而，我们并没有使用预测键系统来做这些。相反，我们有一个概念，叫做“非复制的GameplayCues”。这些GameplayCues只是本地触发，并完全跳过服务器。基本上，所有这些都是对UGameplayCueManager::HandleGameplayCue的直接调用。它们不通过UAbilitySystemComponent，因此没有进行预测键检查或提前返回。
>
> 非复制的GameplayCues的缺点是，它们并没有被复制。因此，确保调用这些函数的代码路径在所有客户端上都能运行就完全依赖于投射物类/蓝图。我们有用于启动（在BeginPlay中调用）、爆炸、撞墙/角色等的cue。
>
> 这些类型的事件已经在客户端生成，所以调用非复制的GameplayCue并不是什么大问题。复杂的蓝图可能会有点棘手，且作者需要确保了解哪些内容在哪些地方运行。

2. **使用`WaitNetSync` `AbilityTask`与`OnlyServerWait`来在本地预测的`GameplayAbility`中创建一个有范围的预测窗口时，玩家是否可能通过延迟他们的网络包来控制`GameplayAbility`的时间？因为服务器正在等待他们的RPC和预测键。这个问题在Paragon或Fortnite中曾经出现过吗？如果出现过，Epic是如何解决的？**

> 是的，这是一个有效的担忧。任何在服务器上运行的能力蓝图，等待客户端的“信号”，都可能容易受到延迟开关类型的利用。
>
> Paragon有一个类似于UAbilityTask_WaitTargetData的自定义目标任务。在这个任务中，我们设置了超时，或者一个“最大延迟”，在客户端即时确认目标模式时等待。如果目标模式需要用户确认（按钮按下），则会忽略这个延迟，因为用户可以自行决定时间。但对于即时确认目标的能力，我们只会等一段时间后，要么A) 在服务器端生成目标数据，要么B) 取消能力。
>
> 我们对于`WaitNetSync`并没有使用类似的机制，且使用得也非常少。
>
> 我不认为Fortnite有使用类似的机制。Fortnite中的武器能力专门进行了批处理，使用单一的Fortnite特定RPC：一个RPC来激活能力，提供目标数据并结束能力。所以在Battle Royale中，武器能力本质上并不容易受到这个问题的影响。
>
> 我的看法是，这个问题可能可以在系统级别解决，但我不认为我们会很快做出这样的更改。针对您提到的情况，为`WaitNetSync`增加最大延迟的修复可能是合理的任务，但同样——我们不太可能在近期内做出这样的修改。

3. **Paragon和Fortnite使用了哪种`EGameplayEffectReplicationMode`，Epic对于何时使用每种模式有什么建议？**

> 两个游戏基本上对于玩家控制的角色使用的是Mixed模式，对于AI控制的角色（AI小兵、丛林怪物、AI僵尸等）使用Minimal模式。这是我建议大多数多人游戏使用系统的人选择的模式。越早在项目中设置这些，效果越好。
>
> Fortnite在优化方面走得更远。它实际上并没有对模拟代理（simulated proxies）复制UAbilitySystemComponent。在::ReplicateSubobjects()中，组件和属性子对象被跳过。我们将Ability System Component中的最小复制数据推送到Pawn本身的结构体中（基本上是属性值的子集和我们在位图中复制的标签的白名单子集）。我们称之为“代理”（proxy）。在接收端，我们将这些数据从Pawn复制到Ability System Component上。所以在FNBR中，每个玩家都有一个ASC，它只是没有直接复制：而是通过Pawn上的最小代理结构进行复制，然后路由回接收端的ASC。这是有优势的，因为它A) 复制的数据量更小，B) 利用Pawn的相关性。
>
> 我不确定随着其他服务器端优化（如Replication Graph等）的出现，这种做法是否仍然必要，而且它也不是最易维护的模式。

4. **由于我们不能预测`GameplayEffects`的移除（根据`GameplayPrediction.h`），是否有任何策略可以缓解延迟对移除`GameplayEffects`的影响？例如，在移除移动速度减慢效果时，我们目前必须等待服务器复制`GameplayEffect`的移除，从而导致玩家角色位置发生瞬移。**

> 这是一个棘手的问题，我没有一个很好的答案。我们通常通过容忍度和平滑处理来避免这些问题。我完全同意能力系统和角色移动系统之间的精确同步目前处于一个不太理想的状态，这是我们希望修复的部分。
>
> 我曾经尝试允许预测性地移除`GameplayEffects`，但在解决所有边界情况之前就不得不放弃了。不过，这并不能解决所有问题，因为角色移动仍然有一个内部的保存移动缓冲区，它并不知道能力系统和可能的移动速度修正等内容。即便不考虑不能预测`GameplayEffects`的移除，仍然有可能进入修正反馈回路。
>
> 如果你认为有一个非常紧急的情况，你可以预测性地添加一个`GameplayEffect`来抑制你的移动速度`GameplayEffect`。我自己从未做过，但曾经理论化过这个方法。它可能有助于解决某些问题。

5. **我们知道在Paragon和Fortnite中，`AbilitySystemComponent`位于`PlayerState`上，而在Action RPG示例中，它位于`Character`上。Epic的内部规则、指南或建议是什么，关于`AbilitySystemComponent`应该放在哪里，`Owner`应该是什么？**

> 一般来说，我认为不需要重新生成的对象应该将`Owner`和`Avatar`设为相同的对象，比如AI敌人、建筑、世界道具等。
>
> 任何需要重新生成的对象，`Owner`和`Avatar`应该是不同的，这样`AbilitySystemComponent`就不需要在重新生成后保存/重新创建/恢复。`PlayerState`是一个逻辑上的选择，它会被复制到所有客户端（而`PlayerController`则不会）。缺点是`PlayerState`总是相关的，所以在100人游戏中可能会遇到问题（参见问题#3中Fortnite的做法）。
以下是您的文本翻译成中文并以Markdown格式呈现的内容：



6. **是否可以让多个 `AbilitySystemComponents` 拥有相同的所有者，但有不同的化身（例如在Pawn和武器/物品/投射物上，`Owner` 设置为 `PlayerState`）？**

> 我看到的第一个问题是如何在拥有的演员上实现 `IGameplayTagAssetInterface` 和 `IAbilitySystemInterface`。前者可能可行：只需从所有 ASC 聚合标签（但要小心——`HasAllMatchingGameplayTags` 可能需要通过跨 ASC 聚合来满足。仅仅将调用转发到每个 ASC 并将结果“或”起来是不够的）。但后者更棘手：哪个 ASC 是权威的？如果有人想应用 GE，应该由哪个 ASC 接收？也许你能解决这些问题，但这一方面的问题将是最难的：所有者下面有多个 ASC。

> 但在Pawn和武器上分开 ASC 可能是合理的。例如，区分描述武器的标签和描述拥有的Pawn的标签。也许确实有道理，授予武器的标签也“应用”于拥有者而不是其他任何东西（例如，属性和 GE 是独立的，但拥有者会聚合它们拥有的标签，像我上面描述的那样）。这应该可以解决，我很确信。但拥有多个具有相同所有者的 ASC 可能会变得复杂。

---

7. **有没有办法阻止服务器覆盖拥有客户端的本地预测能力的冷却时间？在高延迟场景中，这将允许拥有客户端“尝试”再次激活该能力，当它的本地冷却时间过期时，但它仍然在服务器上处于冷却状态。当拥有客户端的激活请求通过网络到达服务器时，服务器可能已经冷却完毕，或者服务器可能能够排队剩余几毫秒的激活请求。否则，像现在这样，具有较高延迟的客户端在重新激活能力时会比延迟较低的客户端有更长的延迟。这在冷却时间极短的能力（例如基本攻击，冷却时间不到一秒）上最为明显。如果没有办法阻止服务器覆盖本地预测能力的冷却时间，Epic 针对高延迟对重新激活能力的影响有何缓解策略？换句话说，Epic 是如何设计《Paragon》的基本攻击和其他能力，以便高延迟玩家能够与低延迟玩家一样快速地攻击或激活（本地预测）？**

> 简短的回答是没有办法防止这种情况，Paragon 确实存在这个问题。较高延迟的连接会导致基本攻击的 ROF 较低。
> 我曾试图通过添加“GE 协调”来解决这个问题，即在计算 GE 持续时间时考虑延迟。实际上，允许服务器消耗一些总的 GE 时间，使得 GE 客户端时间在任何延迟下都能与服务器时间保持一致（尽管波动仍然可能导致问题）。然而，我从未能将其做得足够完善以供发布，项目进展很快，我们也没能完全解决这个问题。
> Fortnite 对武器射击速率进行了自己的账务处理：它不使用 GE 来处理武器的冷却时间。如果这是你游戏的关键问题，我建议你也采用这种方式。

---

8. **Epic 对 GameplayAbilitySystem 插件的路线图是什么？Epic 计划在 2019 年及以后添加哪些功能？**

> 我们认为总体上这个系统目前非常稳定，我们没有人专门致力于开发新的主要功能。偶尔会为 Fortnite 或来自 UDN/拉取请求进行错误修复和小的改进，但现在就只有这些。
> 从长远来看，我认为我们最终会做一个“V2”或进行一些大规模的更改。我们从编写这个系统中学到了很多，感觉做对了很多事情，也犯了不少错误。我很想有机会纠正这些错误，改进一些上面提到的致命缺陷。
> 如果真的出现 V2，提供升级路径将是至关重要的。我们绝不会做一个 V2 然后让 Fortnite 永远停留在 V1：会有一些路径或程序来尽可能自动迁移，尽管仍然几乎肯定需要手动重新构建。
> 高优先级的修复包括：
> * 更好的与角色移动系统的互操作性。统一客户端预测。
> * GE 移除预测（问题 #4）。
> * GE 延迟协调（问题 #7）。
> * 一般的网络优化，如批处理 RPC 和代理结构。主要是我们为 Fortnite 做的工作，但寻找方法将其分解成更通用的形式，至少这样游戏就能更容易地编写自己的游戏特定优化。
>
> 我考虑进行的一些更一般的重构类型的更改包括：
> * 我想从根本上考虑不再让 GE 直接引用电子表格中的数值，而是让它们能够发出参数，这些参数可以由绑定到电子表格值的更高级别对象填充。当前模型的问题是，GE 因为与曲线表行紧密耦合，变得不可共享。我认为可以编写一个通用的参数化系统，并作为 V2 系统的基础。
> * 减少 UGameplayAbility 上的“策略”数量。我会去掉 `ReplicationPolicy` 和 `InstancingPolicy`。我认为复制几乎永远不需要，并且会造成困惑。`InstancingPolicy` 应该被通过将 `FGameplayAbilitySpec` 变成可以子类化的 `UObject` 来替代。这应该是“非实例化的能力对象”，具有事件并可以通过蓝图实现。`UGameplayAbility` 应该是“每次执行时实例化”的对象。如果你需要真正实例化的话，它是可选的：相反，“非实例化”的能力将通过新的 `UGameplayAbilitySpec` 对象来实现。
> * 系统应该提供更多“中层”构造，比如“过滤后的 GE 应用容器”（通过数据驱动将 GE 应用到哪些演员，采用更高层次的游戏逻辑）、“重叠体积支持”（基于碰撞原语重叠事件应用“过滤后的 GE 应用容器”）等。这些是每个项目最终都会以自己方式实现的构建块。做对它们并非小事，所以我认为我们应该提供一些基本实现。
> * 一般来说，减少启动项目所需的模板代码。可能开发一个独立的模块，如“Ex 库”或类似的东西，能够提供诸如被动能力或基础命中扫描武器等功能。这个模块是可选的，但可以让你快速启动项目。
> * 我希望将 GameplayCues 移到一个与能力系统解耦的独立模块中。我认为这里有很多改进的空间。

> 这仅是我的个人观点，并不是任何人的承诺。我认为最现实的做法是，随着新的引擎技术的推进，能力系统将需要更新，到那个时候就会有机会进行这些更改。这些技术可能涉及脚本、网络或物理/角色移动。不过这都属于远期的规划，所以我无法给出承诺或时间预估。


**[⬆ 返回顶部](#table-of-contents)**


<a name="resources-daveratti-community2"></a>
#### 11.1.2 社区问题 2
社区成员 [iniside](https://github.com/iniside) 与 Dave Ratti 的问答：

1. **是否有计划支持解耦的固定时钟？我希望能够让游戏线程保持固定（如 30/60fps），而渲染线程可以自由运行。我想知道这是否是未来的计划，以便对游戏玩法的工作方式做出一些假设。我主要提这个问题，是因为现在物理系统有了固定的异步时钟，这让我想知道系统的其他部分未来会如何工作。我不隐瞒，如果能够拥有固定时钟的游戏线程而不需要固定引擎其余部分的时钟，那将是非常棒的。**

> 目前没有计划解耦渲染帧率和游戏线程的时钟帧率。我认为这个机会已经错过了，因为这些系统的复杂性，以及需要保持与之前引擎版本的向后兼容性。
>
> 相反，我们的方向是拥有一个异步的“物理线程”，它以固定的时钟速率运行，独立于游戏线程。需要以固定速率运行的东西可以在这里运行，游戏线程/渲染可以像以前一样运行。
>
> 值得澄清的是，网络预测支持所谓的独立时钟和固定时钟模式。我的长期计划是保持网络预测中的独立时钟模式大致保持现在的状态，即在游戏线程上以可变帧率运行，并且没有“组/世界”预测，只是经典的“客户端预测自己的棋子和已拥有的演员”模式。固定时钟将使用异步物理功能，允许你预测非客户端控制/拥有的演员，如物理对象、其他客户端/棋子/车辆等。

2. **有没有计划如何将网络预测与能力系统集成？例如，固定帧能力激活（这样服务器就能接收到能力激活的帧以及任务执行的帧，而不是预测键）？**

> 是的，计划是重写/移除能力系统的预测键，并用网络预测的构造替代它们。NetworkPredictionExtras 中的 MockAbility 示例展示了这可能如何工作，但它们比 GAS 需要的更“硬编码”。
>
> 主要的想法是，我们将移除 ASC 的 RPCs 中显式的客户端->服务器预测键交换。将不再有预测窗口或作用域预测键。相反，一切都将围绕网络预测帧进行锚定。重要的是客户端和服务器能够在事情发生时达成一致。例子包括：
>
> * 何时激活/结束/取消能力
> * 何时应用/移除游戏效果
> * 属性值（属性在帧 X 时的值）
>
> 我认为这可以在能力系统层面上通用地完成。但实际上使 UGameplayAbility 中的用户定义逻辑完全可回滚，仍然需要更多的工作。我们可能会有一个 UGameplayAbility 的子类，它是完全可回滚的，并且只访问一个更有限的功能集，或者只有标记为回滚友好的能力任务。类似这样的东西。动画事件、根运动和它们的处理方式也有很多影响。
>
> 希望我能给出一个更明确的答案，但在重新处理 GAS 之前，我们必须确保基础部分正确。运动和物理必须稳固，才能改变更高层次的系统。

3. **是否有计划将网络预测开发转移到主分支？不骗你，我真的很想查看最新的代码，不管它的状态如何。**

> 我们正在朝着这个方向努力。系统工作仍然全部在 NetworkPrediction 中进行（参见 NetworkPhysics.h），而底层的异步物理功能应该都可以使用（例如 RewindData.h 等）。但我们在 Fortnite 中有一些用例，显然不能公开。我们正在处理错误、性能优化等问题。
>
> 更多背景：在这个系统的早期版本中，我们非常专注于“前端”部分——如何定义和编写状态和模拟。在这一过程中我们学到了很多东西。但随着异步物理的上线，我们更多地专注于让这个系统中的某些东西真正工作，而放弃了一些早期的抽象。目标是当真正的东西工作后，回过头来统一这些东西。例如，回到“前端”，在我们现在正在工作的核心技术之上，制作最终版本。

4. **一段时间以来，主分支上有一个用于发送游戏消息的插件（看起来像事件/消息总线），但它被移除了。有没有计划恢复它？考虑到游戏功能/模块化游戏插件，拥有一个通用的事件总线分发器会非常有用。**

> 我想你指的是 GameplayMessages 插件。它可能会在某个时点回来——API 还没有真正定稿，作者并不打算让它公开。我同意它对于模块化游戏设计应该非常有用。但这不是我的领域，所以我没有更多的信息。

5. **最近我一直在尝试异步固定物理，结果很有希望，尽管如果将来有网络预测更新，我可能会等一下再继续玩，因为要使它正常工作，我仍然需要将整个引擎设置为固定时钟，而另一方面，我尝试保持物理在 33ms，这样如果一切都在 30fps 下运行，体验就不好了。**

我注意到有一些关于异步 CharacterMovementComponent 的工作，但不确定它是否会使用网络预测，还是独立的工作？  

既然注意到了这一点，我也去尝试实现了自定义的异步运动，以固定时钟速率运行，效果还不错，但在此基础上我还需要为插值添加单独的更新。设置是将模拟时钟以固定 33ms 更新在单独的工作线程上运行，进行计算，保存结果，并在游戏线程上进行插值，以匹配当前的帧率。不是完美，但完成了工作。

我的问题是，这将来可能会更容易设置吗？因为现在需要写很多样板代码（插值部分），并且为每个移动对象单独插值也不太高效。

异步的东西非常有趣，因为它将允许你以固定更新率运行游戏模拟（这将使固定线程不再必要），并且结果更加可预测。未来会朝着这个方向发展，还是更倾向于选择性系统的好处？据我记得，演员变换并不是异步更新的，蓝图也并不是完全线程安全的。换句话说，这是计划在更高层次的框架中支持，还是每个游戏需要自己解决？

> 异步 CharacterMovementComponent
>
> 这基本上是将 CMC 以其当前形式移植到物理线程的早期原型/实验。我并不认为它是 CMC 的未来，但它可能会发展成那样。目前没有网络支持，所以我不建议过多关注。做这件事的人主要关注的是衡量该系统增加的输入延迟，以及如何缓解这种问题。
>
> 我仍然需要将整个引擎设置为固定时钟速率，同时保持物理在 33ms。这样如果一切都在 30fps 下运行，体验就不好了（：）。
>
> 异步的东西非常有趣，因为它将允许你以固定更新率运行游戏模拟（这将使固定线程不再必要）
>
> 是的。这里的目标是，启用异步物理后，你可以在可变时钟速率下运行引擎，而物理和“核心”游戏模拟可以以固定的速率运行（如角色运动、车辆、GAS 等）。
>
> 目前需要设置的 cvar 如下：（我想你已经弄明白了）
> `p.DefaultAsyncDt=0.03333`
> `p.RewindCaptureNumFrames=64`
>
> Chaos 确实提供了物理状态的插值（例如，推送回 UPrimitiveComponent 并在游戏代码中可见的变换）。现在有一个 cvar，`p.AsyncInterpolationMultiplier`，如果你想查看它，你应该能够看到物理体的平滑连续运动，而不需要写额外的代码。
>
> 如果你想对非物理状态进行插值，目前还是需要自己做。例子就是，如果你想更新（时钟）异步物理线程上的冷却时间，但希望在游戏线程上看到平滑连续的插值，这样每个渲染帧都会更新冷却时间的可视化。我们最终会解决这个问题，但目前没有相关示例。
>
> 需要写大量样板代码
>
> 是的，这是到目前为止系统的一个大问题。我们希望提供一个接口，让有经验的程序员可以用它来最大化性能和安全性（能够编写“只会正常工作”的预测游戏代码，而不是冒着大量风险）。所以，像 CharacterMovement 这样可能会做很多自定义的事情以优化性能——例如编写模板化代码并进行批量更新、并行处理、将更新循环拆分为不同的阶段等等。我们希望提供一个良好的“低级”接口，以便在异步线程和回滚系统中进行这一用例的开发。就这个例子来说——合理的情况是，角色运动系统本身也可以以某种方式进行扩展。例如，提供一种方法来为自定义运动模式提供蓝图支持，并提供一个线程安全的蓝图 API。
>
> 但我们也认识到，对于一些不需要自己“系统”的简单游戏对象来说，这种方式并不合适。更接近 Unreal 的方式才是需要的。例如，使用反射系统，提供通用的蓝图支持等等。现在确实有一些蓝图在其他线程上的使用示例（参见 BlueprintThreadSafe 关键字，以及动画系统在这方面的工作）。所以我认为将来会有某种形式的支持。但是我们现在还没有到达这个阶段。
>
> 我明白你只是在问插值问题，但这是我的总体答案：目前我们要求你手动完成所有操作，比如 NetSerialize、ShouldReconcile、Interpolate 等，但最终我们会提供一种方式，像“如果你只想使用反射系统，你就不需要手动编写这些东西”。我们只是不想 *强制* 所有人都使用反射系统，因为那会引入其他限制，而我们认为在系统的最低层次上不想承担这些限制。
>
> 然后，回到我之前说的——现在我们真的专注于让一些非常具体的示例工作且高效，之后我们会回过头来专注于前端，使事情变得更易于使用和迭代，减少样板代码，为其他所有人提供方便。

**[⬆ 返回顶部](#table-of-contents)**

<a name="changelog"></a>
## 12. GAS 更新日志

这是一个GAS的显著变化列表（修复、改动和新特性），包括了官方Unreal Engine升级更新日志以及我遇到的未记录的变化。如果你发现有未列出的内容，请提一个issue或pull request。

<a name="changelog-5.3"></a>
### 5.3
* 崩溃修复：修复了在无缝旅行后尝试应用Gameplay Cues时崩溃的问题。
* 崩溃修复：修复了在使用Live Coding时，由于GlobalAbilityTaskCount导致的崩溃问题。
* 崩溃修复：修复了UAbilityTask::OnDestroy在递归调用时不会崩溃，适用于像UAbilityTask_StartAbilityState这类情况。
* 错误修复：现在在子类中调用`Super::ActivateAbility`是安全的。之前，它会调用`CommitAbility`。
* 错误修复：添加了正确复制不同类型的FGameplayEffectContext的支持。
* 错误修复：FGameplayEffectContextHandle现在在检索“Actors”之前会检查数据是否有效。
* 错误修复：保留Gameplay Ability System Target Data的旋转信息。
* 错误修复：只有在找到有效PC时，Gameplay Ability System才会停止搜索PC。
* 错误修复：在RemoveGameplayCue_Internal中，如果存在现有的GameplayCueParameters，则使用它，而不是默认的参数对象。
* 错误修复：GameplayAbilityWorldReticle现在朝向源Actor，而不是TargetingActor。
* 错误修复：如果传入了触发事件数据，并且能力列表被锁定，则缓存触发事件数据。
* 错误修复：添加了对FInheritedGameplayTags的支持，使其CombinedTags立即更新，而不是等待保存时更新。
* 错误修复：将ShouldAbilityRespondToEvent从仅客户端代码路径移动到服务器和客户端代码路径。
* 错误修复：修复了FAttributeSetInitterDiscreteLevels在Cooked Builds中无法正常工作的问题，原因是Curve Simplification。
* 错误修复：设置GameplayAbility中的CurrentEventData。
* 错误修复：确保在可能执行回调之前正确设置MinimalReplicationTags。
* 错误修复：修复了ShouldAbilityRespondToEvent未在实例化的GameplayAbility上被调用的问题。
* 错误修复：修复了在gc.PendingKill被禁用时，执行在Child Actors上的Gameplay Cue Notify Actors时内存泄漏的问题。
* 错误修复：修复了GameplayCueManager中由于哈希碰撞，GameplayCueNotify_Actors可能“丢失”的问题。
* 错误修复：即使Actor上没有Gameplay标签，WaitGameplayTagQuery也会遵守其查询条件。
* 错误修复：PostAttributeChange和AttributeValueChangeDelegates现在将具有正确的OldValue。
* 错误修复：修复了FGameplayTagQuery未能正确显示查询描述的问题，尤其是在由本地代码创建结构时。
* 错误修复：确保如果正在使用Ability System，则调用UAbilitySystemGlobals::InitGlobalData。之前如果用户没有调用它，Gameplay Ability System将无法正常工作。
* 错误修复：修复了在链接/取消链接动画层时出现的问题，这些问题发生在UGameplayAbility::EndAbility中。
* 错误修复：更新了Ability System Component函数，检查Spec的能力指针是否在使用之前有效。
* 新特性：添加了GameplayTagQuery字段到FGameplayTagRequirements中，以便可以指定更复杂的需求。
* 新特性：引入了FGameplayEffectQuery::SourceAggregateTagQuery来扩展SourceTagQuery。
* 新特性：扩展了从控制台命令执行和取消Gameplay Abilities和Gameplay Effects的功能。
* 新特性：添加了在Gameplay Ability蓝图上执行“审核”的能力，显示有关其开发和使用意图的信息。
* 改动：OnAvatarSet现在在实例化的每个Actor的主实例上调用，而不是在CDO上调用。
* 改动：允许在同一Gameplay Ability图中同时使用Activate Ability和Activate Ability From Event。
* 改动：AnimTask_PlayMontageAndWait现在有一个切换按钮，允许在BlendOut事件后完成和中断。
* 改动：ModMagnitudeCalc包装函数已声明为const。
* 改动：FGameplayTagQuery::Matches现在对空查询返回false。
* 改动：更新了FGameplayAttribute::PostSerialize，使其包含的属性被标记为可搜索的名称。
* 改动：更新了GetAbilitySystemComponent，使默认参数为Self。
* 改动：将函数标记为虚拟函数，在AbilityTask_WaitTargetData中。
* 改动：移除了未使用的函数FGameplayAbilityTargetData::AddTargetDataToGameplayCueParameters。
* 改动：移除了遗留的GameplayAbility::SetMovementSyncPoint。
* 改动：移除了未使用的复制标志，适用于Gameplay任务和Ability系统组件。
* 改动：将一些Gameplay效果功能移入可选组件中。所有现有内容将在PostCDOCompiled期间自动更新为使用组件（如有必要）。

https://docs.unrealengine.com/5.3/en-US/unreal-engine-5.3-release-notes/

<a name="changelog-5.2"></a>
### 5.2
* 错误修复：修复了`UAbilitySystemBlueprintLibrary::MakeSpecHandle`函数中的崩溃问题。
* 错误修复：修复了在Gameplay Ability System中，一个非控制Pawn会被错误地视为远程Pawn的问题，即使它是在服务器上本地生成的（例如，车辆）。
* 错误修复：正确设置了被服务器拒绝的预测实例能力的激活信息。
* 错误修复：修复了会导致GameplayCues在远程实例上卡住的问题。
* 错误修复：修复了链式调用WaitGameplayEvent时的内存泄漏问题。
* 错误修复：在蓝图中调用AbilitySystemComponent的`GetOwnedGameplayTags()`函数时，不再保留先前调用的返回值。
* 错误修复：修复了GameplayEffectContext复制一个动态对象引用的问题，这个对象永远不会被复制。
  * 这导致GameplayEffect无法调用`Owner->HandleDeferredGameplayCues(this)`，因为`bHasMoreUnmappedReferences`总是为true。
* 新特性：增加了[Gameplay Targeting System](https://docs.unrealengine.com/en-US/gameplay-targeting-system-in-unreal-engine/)的支持，允许通过数据驱动的方式创建目标请求。
* 新特性：为GameplayTag查询添加了自定义序列化支持。
* 新特性：添加了对派生类型FGameplayEffectContext的复制支持。
* 新特性：在资产中，Gameplay Attributes现在会在保存时注册为可搜索的名称，允许在引用查看器中看到属性的引用。
* 新特性：为AbilitySystemComponent添加了一些基本的单元测试。
* 新特性：Gameplay Ability System Attributes现在遵守Core Redirects。这意味着你可以在代码中重命名Attribute Sets和它们的属性，并通过在DefaultEngine.ini中添加重定向条目，确保以旧名称保存的资产能够正确加载。
* 改动：允许从代码中更改Gameplay Effect Modifier的评估通道。
* 改动：移除了以前未使用的变量`FGameplayModifierInfo::Magnitude`。
* 改动：移除了Ability系统组件与Smart Object实例标签之间的同步逻辑。

https://docs.unrealengine.com/5.2/en-US/unreal-engine-5.2-release-notes/

<a name="changelog-5.1"></a>
### 5.1
* 错误修复：修复了复制的松散游戏标签未复制到所有者的问题。
* 错误修复：修复了 AbilityTask 错误，该错误可能导致能力无法及时进行垃圾收集。
* 错误修复：修复了基于标签监听激活的游戏能力无法激活的问题。如果有多个游戏能力监听此标签，并且列表中的第一个无效或没有激活权限，就会发生这种情况。
* 错误修复：修复了正确使用数据注册表的 GameplayEffects 加载时发出警告的问题，并改进了警告文本。
* 错误修复：从 UGameplayAbility 中删除了错误地仅向 Blueprint 调试器注册最后一个实例能力的代码，用于断点。
* 错误修复：修复了如果在 ApplyGameplayEffectSpecToTarget 中的锁定期间调用 EndAbility，游戏能力系统能力会卡住的问题。
* 新增：添加了对游戏效果的支持，以添加阻止的能力标签。
* 新增：添加了 WaitGameplayTagQuery 节点。一个基于 UAbilityTask，另一个基于 UAbilityAsync。此节点指定 TagQuery，并将根据配置在查询变为真或假时触发其输出引脚。
* 新增：修改了控制台变量中的 AbilityTask 调试，以启用调试记录并默认打印到非发布版本中的日志中（可以根据需要打开/关闭修补程序）。
* 新增：您现在可以将 AbilitySystem.AbilityTask.Debug.RecordingEnabled 设置为 0 以禁用，设置为 1 以在非发布版本中启用，设置为 2 以启用所有版本（包括发布版本）。
* 新增：您可以使用 AbilitySystem.AbilityTask.Debug.AbilityTaskDebugPrintTopNResults 仅在日志中打印前 N 个结果（以避免日志垃圾邮件）。
* 新增：STAT_AbilityTaskDebugRecording 可用于测试这些默认调试更改对性能的影响。
* 新增：添加了一个调试命令来过滤 GameplayCue 事件。
* 新增：向游戏能力系统添加了新的调试命令 AbilitySystem.DebugAbilityTags、AbilitySystem.DebugBlockedTags 和 AbilitySystem.DebugAttribute。
* 新增：添加了一个蓝图函数来获取游戏属性的调试字符串表示。
* 新增：添加了一个新的游戏任务资源重叠策略来取消现有任务。
* 更改：现在能力任务应确保仅在对能力指针执行任何需要的操作后才调用 Super::OnDestroy，因为调用后它将被清空。
* 更改：将 FGameplayAbilitySpec/Def::SourceObject 转换为弱引用。
* 更改：将能力任务中的能力系统组件引用设为弱指针，以便垃圾收集可以将其删除。
* 更改：删除了多余的枚举 EWaitGameplayTagQueryAsyncTriggerCondition。
* 更改：GameplayTasksComponent 和 AbilitySystemComponent 现在支持已注册的子对象 API。
* 更改：添加了更好的日志记录以指示 Gameplay 能力无法激活的原因。
* 更改：删除了 AbilitySystem.Debug.NextTarget 和 PrevTarget 命令，转而使用全局 HUD NextDebugTarget 和 PrevDebugTarget 命令。

https://docs.unrealengine.com/5.1/en-US/unreal-engine-5.1-release-notes/

<a name="changelog-5.0"></a>
### 5.0

https://docs.unrealengine.com/5.0/en-US/unreal-engine-5.0-release-notes/

<a name="changelog-4.27"></a>
### 4.27
* 崩溃修复：修复了根运动源问题，即当 Actor 完成使用具有强度随时间变化的修饰符的恒定力根运动任务的能力时，联网客户端可能会崩溃。
* 错误修复：修复了使用 GameplayCues 时编辑器加载时间的回归问题。
* 错误修复：如果设置相同的 EffectLevel，GameplayEffectsContainer 的 `SetActiveGameplayEffectLevel` 方法将不再污染 FastArray。
* 错误修复：修复了 GameplayEffect 混合复制模式中的一种极端情况，即未明确由网络连接拥有但利用 `GetNetConnection` 连接的 Actor 将不会收到混合复制更新。
* 错误修复：修复了 GameplayAbility 类方法“EndAbility”中发生的无限递归，该方法是通过从“K2_OnEndAbility”再次调用“EndAbility”来调用的。
* 错误修复：如果在注册标签之前加载 GameplayTags 蓝图引脚，则它们将不再被默默清除。它们现在的工作方式与 GameplayTag 变量相同，并且可以使用项目设置中的 ClearInvalidTags 选项更改两者的行为。
* 错误修复：改进了 GameplayTag 操作的线程安全性。
* 新功能：将 SourceObject 暴露给 GameplayAbility 的“K2_CanActivateAbility”方法。
* 新功能：原生 GameplayTags。引入了新的“FNativeGameplayTag”，这使得在加载和卸载模块时可以执行正确注册和取消注册的一次性原生标签。
* 新功能：更新了“GiveAbilityAndActivateOnce”以传入 FGameplayEventData 参数。
* 新功能：改进了 GameplayAbilities 插件中的 ScalableFloats，以支持从新的数据注册系统中动态查找曲线表。添加了 ScalableFloat 标头，以便更轻松地在 capabilities 插件之外重用通用结构。
* 新功能：添加了代码支持，用于通过 GameplayTagsEditorModule 在其他编辑器自定义中使用 GameplayTag UI。
* 新功能：修改了 UGameplayAbility 的 PreActivate 方法，以选择性地接收触发事件数据。
* 新功能：添加了更多支持，以使用项目特定的过滤器在编辑器中过滤 GameplayTags。`OnFilterGameplayTag` 提供引用属性和标签源，因此您可以根据请求标签的资产过滤标签。
* 新功能：添加了选项，用于在初始化后调用 GameplayEffectSpec 的类方法 `SetContext` 时保留原始捕获的 SourceTags。
* 新功能：改进了用于从特定插件注册 GameplayTags 的 UI。新的标签 UI 现在允许您为新添加的 GameplayTag 源选择磁盘上的插件位置。
* 新增：在 Sequencer 中添加了一条新轨道，允许在使用 GameplayAbiltiySystem 构建的 Actors 上触发通知状态。与通知一样，GameplayCueTrack 可以利用基于范围的事件或基于触发器的事件。
* 更改：将 GameplayCueInterface 更改为通过引用传递 GameplayCueParameters 结构。
* 优化：对加载和重新生成 GameplayTag 表进行了多项性能改进，以便优化此选项。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS 插件不再被标记为测试版。
* 崩溃修复：修复了在未选择有效标签源的情况下添加游戏标签时发生的崩溃。
* 崩溃修复：将路径字符串参数添加到消息中以修复 UGameplayCueManager::VerifyNotifyAssetIsInValidPath 中的崩溃。
* 崩溃修复：修复了在未检查 ptr 的情况下使用 AbilitySystemComponent_Abilities 时发生的访问冲突崩溃。
* 错误修复：修复了堆叠 GE 时未重置正在应用的效果的其他实例的持续时间的错误。
* 错误修复：修复了导致 CancelAllAbilities 仅取消非实例化能力的问题。
* 新增：向游戏能力提交函数添加了可选标签参数。
* 新增：向 PlayMontageAndWait 能力任务添加了 StartTimeSeconds 并改进了注释。
* 新增：向 FGameplayAbilitySpec 添加了标签容器“DynamicAbilityTags”。这些是随规范复制的可选能力标签。它们也被应用的游戏效果捕获为源标签。
* 新增：GameplayAbility IsLocallyControlled 和 HasAuthority 函数现在可以从蓝图调用。
* 新增：如果我们当前正在记录可视化日志数据，可视化记录器现在将仅收集和存储有关即时 GE 的信息。
* 新增：添加了对蓝图节点中游戏属性引脚上的重定向器的支持。
* 新增：添加了新功能，当根运动移动相关的能力任务结束时，它们将使移动组件的移动模式返回到任务开始前的移动模式。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* 已修复！ UE-92787 使用 Get Float Attribute 节点保存蓝图时崩溃，并且属性引脚是内联设置的
* 已修复！UE-92810 生成 Actor 时崩溃，该 Actor 具有可编辑实例的游戏标记属性，并且该属性标记属性是内联更改的

<a name="changelog-4.25"></a>
### 4.25
* 修复了 `RootMotionSource` `AbilityTasks` 的预测
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) 现在还接受旧的 `Attribute` 值。我们必须将其作为可选参数提供给我们的 `OnRep` 函数。以前，它会读取属性值以尝试获取旧值。但是，如果从复制函数调用，旧值在到达 SetBaseAttributeValueFromReplication 之前已被丢弃，因此我们将获得新值。
* 将 [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) 添加到 `UGameplayAbility`。
* 崩溃修复：修复了在没有有效标签源选择的情况下添加游戏标签时发生的崩溃。
* 崩溃修复：删除了攻击者通过能力系统使服务器崩溃的几种方法。
* 崩溃修复：我们现在确保在检查标签要求之前具有 GameplayEffect 定义。
* 错误修复：修复了游戏标签类别不适用于蓝图中的函数参数（如果它们是函数终止节点的一部分）的问题。
* 错误修复：修复了游戏效果的标签无法通过多个视口复制的问题。
* 错误修复：修复了在循环触发能力时，InternalTryActivateAbility 函数可能使游戏能力规范无效的错误。
* 错误修复：更改了我们在标签计数容器内处理更新游戏标签的方式。当在删除游戏标签的同时推迟父标签的更新时，我们现在将在父标签更新后调用与更改相关的委托。这可确保委托广播时标签表处于一致状态。
* 错误修复：我们现在在确认目标时在内部迭代生成的目标参与者数组之前复制该数组，因为某些回调可能会修改该数组。
* 错误修复：修复了一个错误，即堆叠 GameplayEffects 时，如果未重置所应用效果的其他实例的持续时间，并且由调用者设置了持续时间，则只会为堆栈中的第一个实例正确设置持续时间。堆栈中所有其他 GE 规范的持续时间为 1 秒。添加了自动化测试来检测这种情况。
* 错误修复：修复了处理游戏事件委托修改游戏事件委托列表时可能发生的错误。
* 错误修复：修复了导致 GiveAbilityAndActivateOnce 行为不一致的错误。
* 错误修复：重新排序了 FGameplayEffectSpec::Initialize 中的某些操作，以处理潜在的排序依赖关系。
* 新功能：UGameplayAbility 现在具有 OnRemoveAbility 函数。它遵循与 OnGiveAbility 相同的模式，并且仅在能力的主要实例或类默认对象上调用。
* 新功能：显示被阻止的能力标签时，调试文本现在包含被阻止标签的总数。
* 新增：将 UAbilitySystemComponent::InternalServerTryActiveAbility 重命名为 UAbilitySystemComponent::InternalServerTryActivateAbility。调用 InternalServerTryActiveAbility 的代码现在应调用 InternalServerTryActivateAbility。
* 新增：在添加或删除标签时继续使用过滤器文本显示游戏标签。之前的行为清除了过滤器。
* 新增：在编辑器中添加新标签时不重置标签源。
* 新增：添加了查询能力系统组件以获取具有指定标签集的所有活动游戏效果的功能。新函数称为 GetActiveEffectsWithAllTags，可通过代码或蓝图访问。
* 新增：当根运动移动相关的能力任务结束时，它们现在会将移动组件的移动模式返回到任务开始前的移动模式。
* 新增：使 SpawnedAttributes 成为瞬态，因此它不会保存可能过时和不正确的数据。添加了空检查，以防止任何当前保存的陈旧数据传播。这可以防止与 SpawnedAttributes 中存储不良数据相关的问题。
* API 更改：AddDefaultSubobjectSet 已弃用。应改用 AddAttributeSetSubobject。
* 新功能：游戏能力现在可以指定播放蒙太奇的动画实例。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* 修复了蓝图节点 `Attribute` 变量在编译时重置为 `None` 的问题。
* 需要调用 [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) 才能使用 [`TargetData`](#concepts-targeting-data)，否则您将收到 `ScriptStructCache` 错误，并且客户端将与服务器断开连接。我的建议是现在在每个项目中都调用它，而在 4.24 之前它是可选的。
* 修复了将 `GameplayTag` 设置器复制到没有先前定义变量的蓝图时崩溃的问题。
* `UGameplayAbility::MontageStop()` 函数现在正确使用 `OverrideBlendOutTime` 参数。
* 修复了编辑时未修改组件上的 `GameplayTag` 查询变量的问题。
* 增加了 `GameplayEffectExecutionCalculations` 的功能，以支持针对不需要由属性捕获支持的“临时变量”的范围修饰符。
* 实现基本上允许创建 `GameplayTag` 标识的聚合器，作为执行公开要使用范围修饰符进行操作的临时值的一种方式；您现在可以构建需要可操作值的公式，这些值不需要从源或目标捕获。
* 要使用，执行必须向新成员变量 `ValidTransientAggregatorIdentifiers` 添加标签；这些标签将显示在底部的范围修饰符的计算修饰符数组中，标记为临时变量 - 并相应地更新详细信息自定义以支持功能
* 添加了受限标签生活质量改进。删除了受限 `GameplayTag` 源的默认选项。我们在添加受限标签时不再重置源，以便更轻松地连续添加多个标签。
* `APawn::PossessedBy()` 现在将 `Pawn` 的所有者设置为新的 `Controller`。这很有用，因为如果 `ASC` 位于 `Pawn` 上，则 [混合复制模式](#concepts-asc-rm) 要求 `Pawn` 的所有者是 `Controller`。
* 修复了 `FAttributeSetInitterDiscreteLevels` 中 POD（普通旧数据）的错误。

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ 返回顶部](#table-of-contents)**

