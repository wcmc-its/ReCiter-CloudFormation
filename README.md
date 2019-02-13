# ReCiter-CloudFormation
AWS CloudFormation for ReCiter

##### CloudFormation Resources

1. https://medium.com/boltops/why-generate-cloudformation-templates-with-lono-65b8ea5eb87d
2. https://github.com/stelligent/cloudformation_templates/tree/master/labs/eb
3. CodeBuild useful template: https://www.itonaut.com/2018/06/18/use-github-source-in-aws-codebuild-project-using-aws-cloudformation/

##### CloudFormation Stacks:

1. https://www.terraform.io/guides/index.html
2. https://github.com/cloudtools/stacker
3. https://github.com/cloudtools/troposphere

#### Commonly Used Intrinsic Functions.

##### Fn:ImportValue
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-importvalue.html

Usage: Returns the value of an output exported by another stack. Typically used to create cross-stack references.

##### Ref
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-ref.html

Usage: Returns the value of the specified parameter or resource.

##### Common Errors

1. Pipeline has only 1 stages. There should be a minimum of 2 stages in a pipeline (Service: AWSCodePipeline; Status Code: 400; Error Code: InvalidStructureException; Request ID: 87eedc5c-fa4b-4338-959f-e1e084c6a177)

AWS CloudFormation requires at least 2 stages in the CodePipeline configuration when initializing a stack.

2. CodeBuild Throws Error:
```
Failed to call UpdateProject, reason: Invalid type provided: Project source type (Service: AWSCodeBuild; Status Code: 400; Error Code: InvalidInputException; Request ID: b173239d-2f51-11e9-8d47-2333ebd6fde8)
```
Make sure that `Type: GITHUB` is all upper-cased. So "Github" or "GitHub" will not work.
