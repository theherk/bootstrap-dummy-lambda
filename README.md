# bootstrap-dummy-lambda

This is a simple AWS Lambda starter that can be used for custom runtimes. The usefulness is only realized, for example, if you deploy an initial lambda but manage its updates and ongoing maintenance outside of lambda.

In my case, I create Rust Lambdas using Terraform with this dummy code, then manage versions outside of terraform. Until those are deployed this simply logs `Lambda not installed or initialized.`

> That's all folks.

## Example

Before running terraform, pull or copy this code.

    https://raw.githubusercontent.com/theherk/bootstrap-dummy-lambda/main/bootstrap

Then, you can either zip it or let terraform zip it.

```hcl
data "archive_file" "this" {
  type        = "zip"
  source_dir  = "${path.module}/bootstrap"
  output_path = "${path.module}/bootstrap.zip"
}

resource "aws_lambda_function" "this" {
  filename         = "${path.module}/bootstrap.zip"
  function_name    = "example"
  handler          = "bootstrap"
  memory_size      = 256
  role             = aws_iam_role.this.arn
  runtime          = "provided.al2"
  source_code_hash = data.archive_file.this.output_base64sha256
  timeout          = 30

  lifecycle { ignore_changes = [source_code_hash] }
}
```
