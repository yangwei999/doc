## 背景
robot-access配置了需要转发的仓库和转发的robot地址，两者是多对多的关系。直接在配置文件中关联会产生大量冗余的配置，不利于维护。

为解决此问题，新增了repo_group和plugins_group配置，优化了配置方法。

现对新的配置方法作个简单介绍。

## repo_group
即仓库组，以rg-为前缀命名，定义了使用同一组robot的仓库。

原配置：
```yaml
  repo_plugins:
    mindspore/docs:
      - robot-gitee-mindspore-review-trigger-new

    mindspore/mindspore:
      - robot-gitee-mindspore-review-trigger-new

    mindspore/graphlearning:
      - robot-gitee-mindspore-review-trigger-new
```

等同于：
```yaml
  repo_plugins:
    rg-trigger-new:
      - robot-gitee-mindspore-review-trigger-new

  repo_group:
    rg-trigger-new:
      - mindspore/docs
      - mindspore/mindspore
      - mindspore/graphlearning
```

即使已经在repo_group中定义的repo，也可以进行单独扩展
```yaml
  repo_plugins:
    rg-trigger-new:
      - robot-gitee-mindspore-review-trigger-new
      
    mindspore/docs:
      - robot-gitee-mindspore-label

  repo_group:
    rg-trigger-new:
      - mindspore/docs
      - mindspore/mindspore
      - mindspore/graphlearning
```

## plugins_group
插件组，以pg-为前缀命名，定义了可被一起使用的一组插件。

原配置：
```yaml
  repo_plugins:
    mindspore/docs:
      - robot-gitee-mindspore-label
      - robot-gitee-mindspore-lifecycle
      - robot-gitee-mindspore-assign
      - robot-gitee-mindspore-review-trigger

    mindspore/course:
      - robot-gitee-mindspore-label
      - robot-gitee-mindspore-lifecycle
      - robot-gitee-mindspore-assign
      - robot-gitee-mindspore-review-trigger
```

等同于：

```yaml
  repo_plugins:
    mindspore/docs:
      - pg-a

    mindspore/course:
      - pg-a

  plugins_group:
    pg-a:
      - robot-gitee-mindspore-label
      - robot-gitee-mindspore-lifecycle
      - robot-gitee-mindspore-assign
      - robot-gitee-mindspore-review-trigger
```

group和group之间可以自由组合，还可以单独进行扩展。

```yaml
  repo_plugins:
    mindspore/docs:
      - pg-a
      - pg-b

    mindspore/course:
      - pg-a
      - robot-gitee-mindspore-review-trigger-new

  plugins_group:
    pg-a:
      - robot-gitee-mindspore-label
      - robot-gitee-mindspore-lifecycle
      
    pg-b:
      - robot-gitee-mindspore-assign
      - robot-gitee-mindspore-review-trigger
```
group解析时，会对所有plugins进行去重，不必担心重复的配置。

## repo_group与plugins_group结合
两者可单独使用，也可以结合起来使用，例如：
```yaml
  repo_plugins:
    mindspore/docs:
      - robot-gitee-mindspore-label
      - robot-gitee-mindspore-lifecycle
      - robot-gitee-mindspore-assign
      - robot-gitee-mindspore-review-trigger

    mindspore/course:
      - robot-gitee-mindspore-label
      - robot-gitee-mindspore-lifecycle
      - robot-gitee-mindspore-assign
      - robot-gitee-mindspore-review-trigger

```
可修改为：

```yaml
  repo_plugins:
    rg-a:
      - pg-a
    
    
  repo_group:
    rg-a:
      - mindspore/docs
      - mindspore/course
      

  plugins_group:
    pg-a:
      - robot-gitee-mindspore-label
      - robot-gitee-mindspore-lifecycle
      - robot-gitee-mindspore-assign
      - robot-gitee-mindspore-review-trigger
```
同样支持repo单独扩展和plugins单独扩展。

```yaml
  repo_plugins:
    rg-a:
      - pg-a
      - robot-gitee-mindspore-review-trigger-new

    mindspore/docs:
      - robot-gitee-mindspore-cla

  repo_group:
    rg-a:
      - mindspore/docs
      - mindspore/course

```

## 说明
新逻辑在支持上述配置方式之余，仍然保持**对原来配置方式的兼容**。
使用者可根据业务具体情况合理使用配置方法。
