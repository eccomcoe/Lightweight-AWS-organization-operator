# Code Framework for Executing Automation Scripts Across Multiple Accounts in an AWS Organization Environment

Despite the availability of CloudFormation StackSets for multi-account resource configuration, and solutions built with Amazon Systems Manager, Amazon CodePipeline, Lambda, and other tools for executing automation across multiple accounts, there are still many low-frequency and testing scenarios that cannot be implemented with an infrastructure-as-code approach, or non-frequent tasks that do not justify the use of complex solutions for managing operational tasks. In these cases, using a lightweight code framework to execute automation scripts becomes a more appropriate choice.

Here, a code framework for executing automation scripts across multiple accounts in an AWS Organization environment is provided in the form of a Jupyter notebook, for quick writing, testing, and batch execution of automation tasks.
The choice of Jupyter notebook is due to its provision of a convenient and intuitive python script writing and debugging environment, and the recording of scripts and execution results in one file, making it well-suited for lightweight automation tasks execution.

## Usage Instructions
### Prerequisites:
Configure an automation-role with automation execution permissions in all accounts, it is recommended to use CloudFormation StackSets to push the configuration of the Role.
Also, prepare an initial admin-user or admin-role with the capability to assume the automation-role.
Set up the access_key and secret_key information in the environment where the Jupyter notebook is executed, it is recommended to use tools like aws-vault for profile switching.

### Writing Automation Task Scripts:
Refer to the template below to write automation tasks that need to be executed in each account:
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
    # Logs and results of the task execution can be directly printed out to the notebook
```
It is worth mentioning that, as a best practice, it is recommended to implement the plan parameter, which is used to ensure that no errors occur during large-scale batch execution, adding a dry run mechanism. When the plan parameter is set to True, only the changes to be executed are output. After verifying that the changes to be executed are as expected, the actual operation is performed.

### Executing Automation Tasks
There are two ways to execute the automation tasks written above. One is that the script will automatically use the AWS Organization API to get the list of accounts and iterate through them, you can also set skipaccounts to skip some accounts where automation tasks do not need to be executed.
The other is to directly set the account_list, to execute automation tasks in specified accounts.
Then, set the previously defined taskfunction in the loop as needed.

## Other Application Scenarios
The script can be combined with CloudFormation, Systems Manager Automation, and other tools to achieve more complex execution logic.