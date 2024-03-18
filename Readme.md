# AWS Organization环境下在多个account执行自动化脚本的代码框架

尽管已经有了CloudFormation StackSet进行多账号资源配置，和Amazon Systems Manager，Amazon Codepipeline，Lambda等工具构建的在多个账号执行自动化的解决方案，但仍有不少低频和测试的场景，无法使用基础架构即代码方式实现，或者非高频任务，没有必要使用复杂的解决方案来管理执行的运维任务。这时使用轻量化的代码框架来执行自动化脚本就成为更合适的选择。

这里以Jupyter notebook的形式，提供了一种AWS Organization环境下在多个account执行自动化脚本的代码框架，以供快速编写、测试验证、批量执行自动化任务。
使用Jupyter notebook的考量是其提供了很简便直观的python脚本编写调试环境，并且将脚本与执行结果记录至一个文件中，很适合轻量自动化任务的执行。

## 使用说明
### 前置条件：
在所有account都配置好具备自动化执行权限的automation-role，推荐使用CloudFormation StackSet推送Role的配置。
另外需要准备好具备Assume到automation-role的初始admin-user或admin-role。
在执行jupyter-notebook的环境设置好access_key和secret_key信息，推荐使用aws-vault等工具进行profile切换。

### 编写自动化任务脚本：
参考下面模板编写在每个account中需要执行的自动化任务：
```
def taskfunction(aws_access_key_id, aws_secret_access_key, aws_session_token, region, plan=True):
    session = boto3.Session(
        aws_access_key_id=aws_access_key_id,
        aws_secret_access_key=aws_secret_access_key,
        aws_session_token=aws_session_token,
        region_name=region
    ) 

    # task script
    ...
    # 任务执行的日志和结果可以直接print输出到notebook中
```
需要说明的是，作为最佳实践，推荐实现plan参数，其用途是为了确保大规模批量执行过程中不会出现错误，增加dry run的机制，当设置plan参数为True时，仅输出会执行的变更，在核对确认执行的变更与预期相符后，再实际执行操作。

### 执行自动化任务
有两种方式来执行上面编写的自动化任务。一种是脚本会自动使用AWS Organization API获取account列表，并遍历执行，也可设置skipaccounts跳过其中一些不需要执行自动化任务的account。
另一种是直接设置account_list，在指定的account中执行自动化任务。
其后在循环中设置之前定义的taskfunction即可。

## 其他应用场景
脚本可以结合CloudFormation, Systems Manager自动化等其他工具配合使用，实现更为复杂的执行逻辑。
