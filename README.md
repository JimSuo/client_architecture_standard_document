# 客户端架构标准文档

**Version 1.0-持续更新**

# 示例项目(编辑中)

[链接: https://github.com/JimSuo/FrameworkSample](https://github.com/JimSuo/FrameworkSample)

# CodeReview

## 流程

*   本地DoxyGen校验注释
    
*   Create Request
    

# 文件夹层级规范

# Lua相关

## lua格式规范

遵循LuaPanda使用的lua-fmt

[链接: https://github.com/trixnz/lua-fmt](https://github.com/trixnz/lua-fmt)

## 关于Lua

### 官方指导手册

[链接: https://cloudwu.github.io/lua53doc/manual.html](https://cloudwu.github.io/lua53doc/manual.html)

### 接口查询文档

[链接: https://pgl.yoyo.org/luai/i/\_](https://pgl.yoyo.org/luai/i/_)

### Lua学习参考

[哔哩哔哩: https://player.bilibili.com/player.html?autoplay=false&bvid=BV1vf4y1L7Rb](https://player.bilibili.com/player.html?autoplay=false&bvid=BV1vf4y1L7Rb)

## Cpp与Lua通信

使用slua的CppBinding模板直接导出对应的Lua方法，替代使用反射直接获取使用

[链接: https://github.com/Tencent/sluaunreal/wiki/CppBinding-%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E](https://github.com/Tencent/sluaunreal/wiki/CppBinding-%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)

下面为使用不同方法调用cpp的调用效率(数据来源于官方) :

|   |  蓝图反射方法(Reflection)  |  CppBinding方法(CppBinding)  |
| --- | --- | --- |
|  空函数调用(empty function call)  |  0.541507  |  0.090571  |
|  函数返回int(function return int)  |  0.560052  |  0.090419  |
|  传入int函数返回int(function return int and pass int)  |  0.587115  |  0.097639  |
|  传入Fstring函数返回int(function return FString and pass int)  |  0.930042  |  0.223207  |

## TODO

尝试使用monaco-editor嵌入到本地html实现一个基于html的IDE, 并且在UE中使用编辑器以WebBrowser的形式调用本地html的URL来实现在引擎中进行lua文件编辑, 并将对象文本存储为uasset

预期效果 :

*   可以读取UE的类型文件, 实现自定义的实时的类以及函数代码智能提示等
    
*   在引擎内可实现从挂载对象到lua逻辑脚本的直接跳转与资产对应, 方便定位与查找
    
*   在使用与逻辑调用时lua文件不以外部文件形式出现, 在引擎打pak时不需要额外处理
    
*   依旧有一份文件以lua的形式存储, 作为备份以及方便使用IDE进行断点调试
    

# Unreal相关

## 蓝图

蓝图规范遵循社区推荐规范：

[https://github.com/thejinchao/ue5-style-guide](https://github.com/thejinchao/ue5-style-guide)

## 资产引用

### 相关文章

[https://dev.epicgames.com/documentation/zh-cn/unreal-engine/asynchronous-asset-loading-in-unreal-engine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/asynchronous-asset-loading-in-unreal-engine)

## Cpp详细代码规范

### 代码编写

Cpp代码规范遵循官方指导规范：

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/epic-cplusplus-coding-standard-for-unreal-engine)

### 所有cpp文件通用规则

*   头文件引用排序(特殊顺序引用除外)
    
*   对象以及函数的声明排布
    
*   属性与函数声明
    

对照DoxyGen注释格式标准书写注释

*   Public、Protected、Private权限的声明规则
    
    *   Public要慎重使用，尽量避免使对象的调用列表有冗余的调用项
        
    *   属性声明
        
    *   非必要不使用pulic关键字，使用Get和Set进行获取和调用
    
*   函数声明
    
    *   生命周期相关函数顺序最好按照生命周期顺序声明
        
    *   .cpp与.h文件中函数声明与实现的顺序要一一对应
    
*   引用以及类型使用
    
    *   命名包含的关键字：In, Out, New, Is
        
    *   函数内部使用的变量尽量使用auto代替手动书写类型(指针类型可标记为auto\*)
        

*   格式
    
    *   括号对齐，方便阅读
        
    *   单行代码尽量避免换行，如后面需要添加多行逻辑，恢复为通用的代码块格式
        

    protected:
      // 建议格式
      int32 SampleFucn() const { return 1; }
      // 不建议格式
      int32 SampleFucn() const
      {
        return 1;
      }

*   运算符
    
    *   三目运算符：简单的逻辑可使用三目运算符避免代码块冗余。但是复杂逻辑不使用三目运算符，避免影响代码阅读
        
    

### Class

#### Lifetime

明确类的对象所依存的生命周期

#### 类型继承

*   禁止使用多继承(特殊必要使用要写好说明)防止出现菱形继承，不要用虚继承避免菱形继承，功能行为尽可能使用接口实现
    
*   遵循接口隔离原则，子类不应被强迫实现其不使用的超类(即父类)方法
    

#### 指针使用

##### 非UObject类型

*   非必要时禁止手动new自定义的Class对象
    
*   尽量使用智能指针引用类属性对象
    
*   避免将数据作为 TSharedRef 或 TSharedPtr 参数传到函数，此操作会因增加和减少引用计数而产生开销，可以将引用对象作为`const&`进行传递
    

**UE提供的智能指针类型以及使用情形**

|  智能指针类型  |  适用情形  |
| --- | --- |
|  TSharedPtr（共享指针）  |  共享指针拥有其引用的对象，无限防止该对象被删除，并在无共享指针或共享引用（见下文）引用其时，最终处理其的删除。共享指针可为空白，意味其不引用任何对象。任何非空共享指针都可对其引用的对象生成共享引用。  |
|  TSharedRef（弱指针）  |  共享引用的行为与共享指针类似，即其拥有自身引用的对象。对于空对象而言，其存在不同；共享引用须固定引用非空对象。共享指针无此类限制，因此共享引用可固定转换为共享指针，且该共享指针固定引用有效对象。要确认引用的对象是非空，或者要表明共享对象所有权时，请使用共享引用。  |
|  TWeakPtr（共享引用）  |  弱指针类与共享指针类似，但不拥有其引用的对象，因此不影响其生命周期。此属性中断引用循环，因此十分有用，但也意味弱指针可在无预警的情况下随时变为空。因此，弱指针可生成指向其引用对象的共享指针，确保程序员能对该对象进行安全临时访问。  |
|  UniquePtr（唯一指针）  |  唯一指针仅会显式拥有其引用的对象。仅有一个唯一指针指向给定资源，因此唯一指针可转移所有权，但无法共享。复制唯一指针的任何尝试都将导致编译错误。唯一指针超出范围时，其将自动删除其所引用的对象。  |

:::
对**UniquePtr**引用的对象进行共享指针或共享引用的操作十分危险。即使其他智能指针继续引用该对象，此操作不会取消唯一指针自身被销毁时删除该对象的行为。同样，不应为共享指针或共享引用引用的对象创建唯一指针。
:::

**优点**

*   **防止内存泄露** 共享引用不存在时，智能指针（除弱指针）会自动删除对象
    
*   **弱引用** 弱指针会中断引用循环并组织悬挂指针
    
*   **可选择的线程安全** 提供线程安全代码，可跨线程管理引用计数。并且可以自由开关
    
*   **运行时安全** 共享引用从不为空，可固定随时取消引用
    
*   **授予意图** 可轻松区分对象所有者和观察者
    
*   **内存** 智能指针在64位下为C++指针大小的两倍（UniquePtr除外，其与C++指针大小相同）
    

##### 相关文档

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/smart-pointers-in-unreal-engine?application\_version=5.4](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/smart-pointers-in-unreal-engine?application_version=5.4)

[https://www.cnblogs.com/shiroe/p/14729821.html](https://www.cnblogs.com/shiroe/p/14729821.html)

##### UObject类型

*   共享指针与UObject及其衍生类不兼容。
    

### Enum

### Struct

:::
**用于存储纯数据对象**
:::

如果存储的数据需要有对应的函数，有以下两种处理方式

*   不使用Struct实现转而使用Class实现
    
*   在使用Struct的Class中实现
    

### Interface

*   遵循接口隔离原则，继承接口的类不应被强迫实现其不使用的方法。
    

### 注释

*   Cpp注释遵循DoxyGen注释标准：[《\*Doxygen注释规范》](https://alidocs.dingtalk.com/i/nodes/l6Pm2Db8D4qNvndwtzwPeEpM8xLq0Ee4?corpId=)
    
*   函数内注释
    
    *   调用的函数可以不需要书写注释（因为函数本身有注释说明）
        
    *   无法被直接理解的逻辑块需要添加注释
        
    *   明确指出对象为拷贝/引用/指针引用
        

    void SampleClass::SampleFuc1()
    {
      // 无需添加注释
      sampleFunc2();
      // 需要添加注释, 说明逻辑意图
      TArray<int32> SampleArr = {1, 2, 3, 4};
      for (int i = 0; i < SampleArr.Num(); ++i)
      {
        SampleArr[1] += 1;
      }
    }

# Config

静态配置可以通过在Config中添加配置字段并通过带有config宏标记的UObject类型作为对应的存储

    UCLASS(Blueprintable, config = Game)
    class FRAMEWORKSAMPLE_API UCustomConfigFile : public UObject
    {
    	GENERATED_BODY()
    }

动态配置可以使用DataAsset来实现，在类应用中使用`TWeakObjectPtr<DataAsset>`引用即可

## 相关文档

[链接: https://dev.epicgames.com/documentation/en-us/unreal-engine/configuration-files-in-unreal-engine](https://dev.epicgames.com/documentation/en-us/unreal-engine/configuration-files-in-unreal-engine)

[UE4/UE5 自定义Config文件](https://zhuanlan.zhihu.com/p/591615263)UE4/UE5 自定义Config文件

[https://blog.csdn.net/hhw\_hhw/article/details/132204304](https://blog.csdn.net/hhw_hhw/article/details/132204304)

# CDO

相关逻辑位于Obj.cpp

[链接: https://forums.unrealengine.com/t/what-is-cdo/310820](https://forums.unrealengine.com/t/what-is-cdo/310820)

# Gameplay

严格基于UE的Gameplay框架的生命周期函数实现逻辑

## 生命周期

### TheUnrealEngineGameFramework: From int main() to BeginPlay()

[YouTube: https://www.youtube.com/embed/IaU2Hue-ApI?feature=shared](https://www.youtube.com/embed/IaU2Hue-ApI?feature=shared)

## 内建Gameplay框架

### 类继承关系

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/1GXn4BW0a3dDODQ4/img/149867fd-f60c-45c7-b9f1-6ab98e70daa9.png)

### 类调用关系

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/1GXn4BW0a3dDODQ4/img/76f6ae25-05a6-434c-99f0-5dffc4293030.png)

​						引用自知乎：@大钊

### 生命周期与使用细则

#### Actor

主要有四个初始化入口

*   从磁盘中加载
    
*   PIE运行
    
*   动态Spawn生成
    
*   延迟Spawn生成
    

##### 相关文档

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-actor-lifecycle?application\_version=5.4](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-engine-actor-lifecycle?application_version=5.4)

#### ActorComponent

`UActorComponent`是所有组件的基类。由于组件是**渲染网格体和图像**、**实现碰撞**和**播放音频**的唯一方法，因此玩家游戏期间在场景中看到或进行交互的一切其实都是某一类组件的成果

##### 一些组件的主要类

*   **Actor组件**`**(UActorComponent)**` : 适用于抽象行为。例如移动、物品栏或属性管理以及其他非物理概念。ActorComponente不具有Transform，即他们在场景中不存在位置或旋转
    
*   **场景组件**`**(USceneComponent)**` : `UActorComponent`的子项，不具有几何标识。支持基于位置的行为，例如弹簧臂、摄像机、物理力和约束(不包括物理对象)，甚至音频
    
*   **Primitive组件**`**(PrimitiveComponent)**` : `USceneComponent`的子项，具有几何标识的场景组件。通常用于渲染视觉元素或与物理对象发生碰撞或重叠。例如静态或骨架网格体、Sprite或公告板、粒子系统以及各种形状的碰撞体积
    

##### Lifetime

*   **RegisterComponent**
    
    *   **UnregisterComponent**
        
        *   **TickComponent** : 默认情况下，Actor组件不更新。为了让Actor组件逐帧更新，必须在构造函数中将`PrimaryComponentTick.bCanEverTick`设置为 true 来启用tick。之后，在构造函数中或其他位置处，必须调用`PrimaryComponentTick.SetTickFunctionEnable(true)`以开启更新。之后可调用`PrimaryComponentTick.SetTickFunctionEnable(false)`停用tick。如果您知道组件永远不需要更新，或者打算手动调用自己的更新函数（也许从拥有的Actor类），将`PrimaryComponentTick.bCanEverTick`保留为默认值 false 即可，这样可以稍微改善性能。
            

##### Render State

*   可以使用`MarkRenderStateDirty`函数将渲染数据标记为dirty。在一帧结束时，所有dirty组件的渲染数据都会在引擎中更新
    
    *   场景组件（包括Primitive组件）默认会创建渲染状态，而Actor组件则不会
        

##### Physic State

*   物理状态会在发生变化时立即更新，为防止"帧落后"瑕疵等问题，也不需要"dirty"标记
    
    *   默认情况下，Actor组件和场景组件没有物理状态，但是Primitive组件有
        
        *   重写`ShouldCreatePhysicsState`函数以确定组件类示例是否需要物理状态
            

:::
如果类使用物理，则不建议只返回`true`。请参阅函数的`UPrimitiveComponent`版本，了解不应创建物理状态的情况（例如在组件破坏期间）。在正常返回`true`的情况下，还可以返回`Super::ShouldCreatePhysicsState`。
:::

##### 视觉化组件

*   要创建视觉化组件，需创建常规组件并在其上方调用`SetIsVisualizationComponent`
    
    *   由于组件无需存在于编辑器之外，所有对它的引用都应当处在对`WITH_EDITORONLY_DATA`或  
        `WITH_EDITOR`的预处理器检查之中。这将确保打包版本不受这些组件的影响，并保证不会在代码中的任何位置引用它们
        

**示例**

摄像机组件使用多个其他组件来在编辑器中显示实用信息，包括用于显示视图视锥的绘制视锥组件。在头文件中，绘制视锥组件在类中进行如下定义 :

    #if WITH_EDITORONLY_DATA
      // 用于显示摄像机视野所在位置的视锥组件
      class UDrawFrustumComponent* DrawFrustum;
      // ...
    #endif

同样在.cpp文件中对这个组件的所有引用应当位于源文件中对`WITH_EDITORONLY_DATA`的预处理器检查之中。`OnRegister`中的`WITH_EDITORONLY_DATA`检查内部的这段代码，将检查确认摄像机组件是否连接到有效Actor，然后添加绘制视锥组件代码：

    void UCameraComponent::OnRegister()
    {
      #if WITH_EDITORONLY_DATA
      if (AActor* MyOwner = GetOwner())
      {
        // ...
        if (DrawFrustum == nullptr)
        {
          DrawFrustum = NewObject<UDrawFrustumComponent>(MyOwner, NAME_None, RF_Transactional | RF_TextExportTransient);
          DrawFrustum->SetupAttachment(this);
          DrawFrustum->SetIsVisualizationComponent(true);
          // ...
        }
      }
      // ...
      #endif
      Super::OnRegister();
      // ...在此处编写其他代码（在所有版本中运行）...
    } 

`DrawFrustum`现仅存在于编辑器中，被视为视觉化组件，即在编辑器中进行游戏测试时不会显示。

##### 附加

*   只有场景组件（`USceneComponent`及其子类）可以彼此附加，因为需要变换来描述子项和父项组件之间的空间关系。
    
    *   场景组件系统不支持附加循环。两种主要方法分别是`SetupAttachment`(在构造函数中、以及处理尚未注册的组件时十分实用)和`AttachToComponent`(立即将场景组件附加到另一个组件，在游戏进行中十分实用)。
        

##### 相关文档

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/components-in-unreal-engine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/components-in-unreal-engine)

#### GameMode

*   复写的GameMode基类为`AGameModeBase`
    
*   基于GameMode自身的生命周期节点函数实现逻辑功能
    
*   使用带有Experience拓展的GameMode，只需动态替换Experience内数据即可实现对于地图，UI，玩家输入，角色技能以及一些游戏规则等自定义的配置
    

![客户端架构标准文档-流程图](https://github.com/JimSuo/client_architecture_standard_document/blob/master/Image/%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%9E%B6%E6%9E%84%E6%A0%87%E5%87%86%E6%96%87%E6%A1%A3-%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg)

++在编辑器内点击Play按键直到BeginPlay()执行流程中的GameMode的关键初始化时序图++

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/VGoewePyTvrABmon/5b697e2942d84708b58940717c1d7a7a3989.png)

++在编辑器内点击Play按键直到BeginPlay()执行流程中的GameMode的关键初始化流程图++

#### GameState

##### 相关文档

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/game-mode-and-game-state-in-unreal-engine?application\_version=5.4](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/game-mode-and-game-state-in-unreal-engine?application_version=5.4)

#### GameAI

##### StateTree(是否使用StateTree替换传统的BehaviourTree)

##### Gameplay Interactions Plugin

#### Movement

##### Character Mover 2.0 (Experimental)

**相关文档**

[链接: https://vorixo.github.io/devtricks/mover-show/](https://vorixo.github.io/devtricks/mover-show/)

[链接: https://github.com/daftsoftware/FGMovement](https://github.com/daftsoftware/FGMovement)

#### Gameplay Interactions Plugin

## GameplayTag

客户端类型判断减少使用枚举，使用GameplayTag作为类型区分的判断

### 相关文档

[链接: https://jambax.co.uk/using-gameplay-tags-in-cpp/](https://jambax.co.uk/using-gameplay-tags-in-cpp/)

# World&Level

## World

## Level

### LevelStreaming

#### World Composition

##### 相关文档

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/world-composition-in-unreal-engine?application\_version=5.4](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/world-composition-in-unreal-engine?application_version=5.4)

# Animation

## 动画预算分配器

### 相关文档

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/animation-budget-allocator-in-unreal-engine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/animation-budget-allocator-in-unreal-engine)

## 运动匹配

### Motion Matching

#### 配合AI工具

Motorica

[链接: https://www.motorica.ai/](https://www.motorica.ai/)

#### 相关文档

[链接: https://dev.epicgames.com/community/learning/tutorials/lwlG/unreal-engine-your-first-60-minutes-with-motion-matching](https://dev.epicgames.com/community/learning/tutorials/lwlG/unreal-engine-your-first-60-minutes-with-motion-matching)

[链接: https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-animation-sample-project-in-unreal-engine)

[链接: https://dev.epicgames.com/documentation/en-us/unreal-engine/motion-matching-in-unreal-engine](https://dev.epicgames.com/documentation/en-us/unreal-engine/motion-matching-in-unreal-engine)

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/motion-warping-in-unreal-engine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/motion-warping-in-unreal-engine)

## 优化

### 相关文档

[动画优化](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/animation-optimization-in-unreal-engine)

# Audio

## Wwise

### 相关文档

[https://www.audiokinetic.com/zh/wwise/overview/](https://www.audiokinetic.com/zh/wwise/overview/)

# Subsystem

Subsystems允许从已经预定义的5个类中继承下来，并自动实例化和托管生命周期。 当在代码里发现要写一个Manager类的时候，就可以使用Subsystem来实现。建议尽量使用Subsystem来管理模块。

## TODO

Lua在继承自Subsystem时候获得的指针为空

## 相关文档

[《Subsystem详解》](https://alidocs.dingtalk.com/i/nodes/PwkYGxZV3Z56OKM0tAd6Kzg6WAgozOKL)

[《DynamicSubsystem详解》](https://alidocs.dingtalk.com/i/nodes/9E05BDRVQ2BbaqrQtNrmRlvXJ63zgkYA?utm_scene=team_space)

# UMG框架

## UMG本身

UMG生命周期

[链接: https://www.cnblogs.com/sin998/p/15490311.html](https://www.cnblogs.com/sin998/p/15490311.html)

## CommonUI

UI整体框架包含CommonUI

~~基于UE官方的建议，由于项目内大部分交互界面都为3DWidget，舍弃使用。官方文档原文：~~

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/design-guidelines-for-using-commonui-in-unreal-engine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/design-guidelines-for-using-commonui-in-unreal-engine)CommonUI

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/1GXn4BW0a3dDODQ4/img/2bd998fe-ff0b-4b89-8241-5b869fde4199.png)

## MVVM(Model-View-ViewModel)

使用MVVM进行 界面-数据 管理

### MVVM数据与界面同步的逻辑示意

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/VGoewePyTvrABmon/135f6775519341fbbd480dfcb34be02d3989.png)

### 使用须知

*   不要将玩家数据存储在ViewModel中，ViewModel仅为同步Widget界面的数据，在玩家的数据被修改时修改对应的ViewModel数据即可
    
*   ViewModel可嵌套使用，尽量使用ViewMode将数据进行分类，再将Widget需要的所有ViewModel作为对象存储在一个ViewModel中，方便更清晰的访问数据与设置绑定
    
*   界面的ViewModel创建类型如无特殊要求尽量不使用Create Instance具体每个类型的说明如下：
    

|  Viewmodel创建类型（Creation Type）  |  说明  |
| --- | --- |
|  创建实例（Create Instance）  |  该控件会自动创建它自己的Viewmodel实例。  |
|  手动（Manual）  |  该控件在初始化时Viewmodel为null，你需要手动创建一个实例并为其赋值。  |
|  全局Viewmodel集合（Global Viewmodel Collection）  |  指你的项目中的所有控件均可使用的全局可用Viewmodel。需要 全局Viewmodel标识符 。  |
|  属性路径（Property Path）  |  在初始化时，执行一个函数来查找Viewmodel。Viewmodel属性路径 将使用句点分隔的成员名称。例如：GetPlayerController.Vehicle.ViewModel。属性路径始终是相对于控件的路径。  |

*   ViewModel与Widget的绑定在蓝图中使用ViewBinding进行，再绑定时尽量实现类型对应的绑定，不使用ViewBinding提供的对象转换函数
    

### 相关文档

[链接: https://dev.epicgames.com/documentation/en-us/unreal-engine/umg-viewmodel%3Fapplication\_version%3D5.1%3Fapplication\_version%3D5.1%3Fapplication\_version%3D5.1%3Fapplication\_version%3D5.1](https://dev.epicgames.com/documentation/en-us/unreal-engine/umg-viewmodel%3Fapplication_version%3D5.1%3Fapplication_version%3D5.1%3Fapplication_version%3D5.1%3Fapplication_version%3D5.1)

[链接: https://blog.csdn.net/zolo\_mario/article/details/134142104](https://blog.csdn.net/zolo_mario/article/details/134142104)

[链接: https://learn.microsoft.com/en-us/archive/msdn-magazine/2009/february/patterns-wpf-apps-with-the-model-view-viewmodel-design-pattern](https://learn.microsoft.com/en-us/archive/msdn-magazine/2009/february/patterns-wpf-apps-with-the-model-view-viewmodel-design-pattern)

## FrameworkUMGModule

> 框架的UMG相关处理模块

[《FrameworkUMG模块》](https://alidocs.dingtalk.com/i/nodes/nYMoO1rWxaOXzBAefONnrQZvV47Z3je9)

# 基于GameFeature封装功能

## 前提

与Plugin相比GameFeature更倾向于实现“玩法”

## 特性

*   可独立开发与测试
    
*   代码隔离，易于单元测试，且更易读
    
*   便于分布式开发
    
*   容易管理与迭代更新，对主体程序影响较小
    
*   容易迁移
    
*   可与热更结合
    

## 关键对象

### GameFrameworkComponentManager

*   将受体Actor注册为接收者，使AddComponent生效
    
*   即使是在GameFeature生效之后再事后注册Actor为Receiver，也能成功的AddComponent
    

### UDefaultGameFeaturesProjectPolicies

*   用于定义GameFeature的加载策略
    

### GameFeatureSubsystem

### GameFeatureAction

*   其中最重要的Action莫过于AddComponents
    

## 与SLua的结合使用

### LuaFilePath

目前在读取文件时，通过标识(识别标识为`$`, 写在LuaFilePath前)来识别是否为GameFeature插件的Lua文件。

由于需要特殊字符作为检测所以需要手动重写LuaFilePath，`GameFeatureHelperMacro.h`提供了构造GameFeature的Lua文件路径的宏方法，方便快速重新构建路径。

    /**
    * 以一个LuaActorComponent类型的类为示例
    */
    UCLASS()
    class MYGAMEFEATURE_API UTestActorComponent : public ULuaActorComponent, public ILuaOverriderInterface
    {
      GENERATED_BODY()
    
      UTestActorComponent(const FObjectInitializer& ObjectInitializer);
    
      // 重写获取LuaFilePath
      // MyGameFeatureName为自定义GameFeature名称
      GET_GAME_FEATURE_LUA_PATH(MyGameFeatureName)
    };

## 使用须知

*   不要更改默认的目录，GameFeature必须放在GameFeatures目录下
    
*   GameFeatureData的名字必须保持跟GameFeature的插件同名
    
*   必须要在Project Settings中配置Game-AssetManager中的Directories为`/Game/Unused`
    
*   Active状态是无法Edit Plugin信息的，编辑完GameFeatureData后需要触发重新加载一下来生效
    
*   必须将Actor注册为Receiver，AddComponent才能工作
    
*   必须设置为激活后，GameFeature才能工作
    
*   GameFeature的状态在编辑器模式下依然存续，因此可能会跨游戏依然存在，需要手动取消激活状态
    
*   使用UDefaultGameFeaturesProjectPolicies重写`InitGameFeatureManager()`自定义加载策略时，不要使用Super调用基类的`InitGameFeatureManager()`且必须在最后调用  
    `UGameFeaturesSubsystem::Get().LoadBuiltInGameFeaturePlugins(AdditionalFilter);`
    

## 相关文档

[https://x157.github.io/UE5/GameFeatures/](https://x157.github.io/UE5/GameFeatures/)

[https://x157.github.io/UE5/ModularGameplay/](https://x157.github.io/UE5/ModularGameplay/)

[链接: https://dev.epicgames.com/documentation/en-us/unreal-engine/game-features-and-modular-gameplay-in-unreal-engine?application\_version=5.1](https://dev.epicgames.com/documentation/en-us/unreal-engine/game-features-and-modular-gameplay-in-unreal-engine?application_version=5.1)

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/game-framework-component-manager-in-unreal-engine?application\_version=5.1](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/game-framework-component-manager-in-unreal-engine?application_version=5.1)

[链接: https://zhuanlan.zhihu.com/p/467236675](https://zhuanlan.zhihu.com/p/467236675)

# 基于Plugin封装功能

## 前提

Plugin用于实现相比GameFeature更加基础的功能，比如网络通信SDK等

# 网络同步

## Multiplayer : How to Understand Network Replication

[YouTube: https://www.youtube.com/embed/JOJP0CvpB8w?feature=shared](https://www.youtube.com/embed/JOJP0CvpB8w?feature=shared)

## Replication&RPC

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/1GXn4BW0a3dDODQ4/img/940b8193-3157-4275-b1a6-0d9a8020908e.png)

### Replicated

Actor、Pawn和角色的一些常见功能不会复制

*   骨骼网格体组件
    
*   静态网格体组件
    
*   材质
    
*   动画蓝图
    
*   粒子系统组件
    
*   声音发射器
    
*   物理对象
    

## 新特性

### Iris Replication

#### 相关文档

[Iris Replication System](https://zhuanlan.zhihu.com/p/638755462)

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/iris-replication-system-in-unreal-engine?application\_version=5.4](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/iris-replication-system-in-unreal-engine?application_version=5.4)

[链接: https://dev.epicgames.com/community/learning/tutorials/z08b/unreal-engine-iris-faq](https://dev.epicgames.com/community/learning/tutorials/z08b/unreal-engine-iris-faq)

[https://www.bilibili.com/read/cv33834717/?jump\_opus=1](https://www.bilibili.com/read/cv33834717/?jump_opus=1)

# Physic

## NetworkPhysic

### 相关文档

[链接: https://vorixo.github.io/devtricks/phys-prediction-use/](https://vorixo.github.io/devtricks/phys-prediction-use/)

# EnhanceInput

# GAS

## GAS技能框架

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/1GXn4BW0a3dDODQ4/img/9d4b7220-d492-4a9a-b70f-9414e36f5365.png)

## Runtime

相关文档

[链接: https://vorixo.github.io/devtricks/lazy-loading-asc/](https://vorixo.github.io/devtricks/lazy-loading-asc/)

## Debug

### 相关文档

[链接: https://portal.productboard.com/epicgames/1-unreal-engine-public-roadmap/c/1477-gas-debugging-improvements?&utm\_medium=social&utm\_source=starter\_share](https://portal.productboard.com/epicgames/1-unreal-engine-public-roadmap/c/1477-gas-debugging-improvements?&utm_medium=social&utm_source=starter_share)

# Mod

## ChunkDownloader

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/setting-up-the-chunkdownloader-plugin-in-unreal-engine?application\_version=5.4](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/setting-up-the-chunkdownloader-plugin-in-unreal-engine?application_version=5.4)

# UnrealEngine编辑器拓展

基于TAPython插件使用python构造编辑器内窗口, 并且调用UE5的Python命令实现需求功能。

## TAPython

### 相关文档

[链接: https://github.com/cgerchenhp/UE\_TAPython\_Plugin\_Release](https://github.com/cgerchenhp/UE_TAPython_Plugin_Release)

[链接: https://www.tacolor.xyz/pages/TAPython.html](https://www.tacolor.xyz/pages/TAPython.html)

# 关于版本控制

## SVN

## Perforce(调研)

## UnrealGameSync (UGS)(调研)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/1GXn4BW0a3dDODQ4/img/306a91c5-9e68-4762-bb4e-2d243861e8c3.png)

### 相关文档

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-game-sync-ugs-for-unreal-engine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/unreal-game-sync-ugs-for-unreal-engine)

# Debug调试

## Animation Insights

使用Animation Insights来观察和分析你的项目在运行期间的游戏和动画性能。

### 相关文档

[链接: https://dev.epicgames.com/documentation/zh-cn/unreal-engine/animation-insights-in-unreal-engine](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/animation-insights-in-unreal-engine)

## Rewind调试器

通过Rewind调试器，你可以录制项目的实时片段并保留数据用于调试工作流程。

### 相关文档

[链接: https://dev.epicgames.com/documentation/en-us/unreal-engine/animation-rewind-debugger-in-unreal-engine](https://dev.epicgames.com/documentation/en-us/unreal-engine/animation-rewind-debugger-in-unreal-engine)

## GameplayDebug

### 快速添加GameplayDebug信息

可使用框架实现的FrameworkGameplayDebugger模块快速添加Debug信息

[《FramworkGameplayDebugger模块》](https://alidocs.dingtalk.com/i/nodes/yQod3RxJKGLakyEriwN0gAvKJkb4Mw9r)

### 相关文档

[Gameplay调试程序](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/using-the-gameplay-debugger-in-unreal-engine?application_version=5.3)

[链接: https://blog.csdn.net/maxiaosheng521/article/details/103280877](https://blog.csdn.net/maxiaosheng521/article/details/103280877)

## Unreal Insights

### 相关文档

[unreal-insights-in-unreal](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-insights-in-unreal-engine?application_version=5.3)

[](https://dev.epicgames.com/documentation/en-us/unreal-engine/unreal-insights-in-unreal-engine?application_version=5.3)

# 零碎的注意事项

*   指针对象的判断，书写形式为 if( objectPtr != nullptr ) 而不是直接使用 if( objectPtr )
    
*   new C++对象 切记要 delete，并在delete后将对应的指针置空，防止出现野指针
    

# 需求&建议收集

[请至钉钉文档查看附件《框架需求&建议收集》](https://alidocs.dingtalk.com/i/nodes/bva6QBXJwadk2BALfvnBMBqeWn4qY5Pr?iframeQuery=anchorId%3DX02lx9veglare04hqq645a&utm_medium=wiki_feed_notification&utm_source=im_SYSTEM)