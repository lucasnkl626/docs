---
title: Integrating AI models into your development workflow
intro: 'Call AI models in the tools you use every day.'
versions:
  feature: github-models
shortTitle: Integrate AI models
redirect_from:
  - /github-models/integrating-ai-models-into-your-development-workflow
---

With {% data variables.product.prodname_github_models %} extensions, you can call specific AI models from both {% data variables.copilot.copilot_chat_short %} and {% data variables.product.prodname_cli %}. These extensions integrate directly into your development workflow, allowing you to prompt models without context switching.

## Using AI models in {% data variables.copilot.copilot_chat_short %}

If you have a {% data variables.product.prodname_copilot_short %} subscription, you can work with AI models in {% data variables.copilot.copilot_chat_short %} in two different ways:
* Using the {% data variables.product.prodname_github_models %} {% data variables.copilot.copilot_extension_short %}. With this extension, you can ask for model recommendations based on certain criteria and chat with specific models. See [Using the {% data variables.product.prodname_github_models %} {% data variables.copilot.copilot_extension_short %}](#using-the-github-models-copilot-extension).
* Using multiple model support in {% data variables.copilot.copilot_chat_short %}. With multi-model {% data variables.copilot.copilot_chat_short %}, you can choose a specific model to use for a conversation, then prompt {% data variables.copilot.copilot_chat_short %} as usual. See [AUTOTITLE](/copilot/using-github-copilot/ai-models/changing-the-ai-model-for-copilot-chat).

### Using the {% data variables.product.prodname_github_models %} {% data variables.copilot.copilot_extension_short %}

> [!NOTE] The {% data variables.product.prodname_github_models %} {% data variables.copilot.copilot_extension_short %} is in {% data variables.release-phases.public_preview %} and is subject to change.

1. Install the [{% data variables.product.prodname_github_models %} {% data variables.copilot.copilot_extension_short %}](https://github.com/marketplace/models-github).
     * If you have a {% data variables.copilot.copilot_pro_short %} subscription, you can install the extension on your personal account.
     * If you have access to {% data variables.product.prodname_copilot_short %} through a {% data variables.copilot.copilot_business_short %} or {% data variables.copilot.copilot_enterprise_short %} subscription:
       * An organization owner or enterprise owner needs to enable the {% data variables.copilot.copilot_extensions_short %} policy for your organization or enterprise.
       * An organization owner needs to install the extension for your organization.

1. Open any implementation of {% data variables.copilot.copilot_chat_short %} that supports {% data variables.copilot.copilot_extensions %}. For a list of supported {% data variables.copilot.copilot_chat_short %} implementations, see [AUTOTITLE](/copilot/using-github-copilot/using-extensions-to-integrate-external-tools-with-copilot-chat#supported-clients-and-ides).
1. In the chat window, type `@models YOUR-PROMPT`, then send your prompt. There are several use cases for the {% data variables.product.prodname_github_models %} {% data variables.copilot.copilot_extension_short %}, including:
    * Recommending a particular model based on context and criteria you provide. For example, you can ask for a low-cost OpenAI model that supports function calling.
    * Executing prompts using a particular model. This is especially useful when you want to use a model that is not currently available in multi-model {% data variables.copilot.copilot_chat_short %}.
    * Listing models currently available through {% data variables.product.prodname_github_models %}

## Using AI models with {% data variables.product.prodname_actions %}

You can use the {% data variables.product.prodname_actions %} token (`GITHUB_TOKEN`) to call AI models directly inside your workflows.

### Setting permissions

To use AI models in your workflows, ensure that the `models` permission is enabled in your workflow configuration. This permission allows workflows to access the {% data variables.product.prodname_github_models %} inference API. You can either set this permission itself or use the general `read-all` or `write-all` permissions. See [AUTOTITLE](/rest/overview/permissions-required-for-github-apps?apiVersion=2022-11-28#repository-permissions-for-actions).

### Writing your workflow file

You can call the inference API directly from your workflow. For instance:

```yaml
name: Use GitHub Models

on:
  workflow_dispatch:

permissions:
  models: read

jobs:
  call-model:
    runs-on: ubuntu-latest
    steps:
      - name: Call AI model
        env:
          GITHUB_TOKEN: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
        run: |
          curl "https://models.github.ai/inference/chat/completions" \
             -H "Content-Type: application/json" \
             -H "Authorization: Bearer $GITHUB_TOKEN" \
             -d '{
              "messages": [
                  {
                     "role": "user",
                     "content": "Explain the concept of recursion."
                  }
               ],
               "model": "openai/gpt-4o"
            }'
```

## Using AI models from the command line

> [!NOTE] The {% data variables.product.prodname_github_models %} extension for {% data variables.product.prodname_cli %} is in {% data variables.release-phases.public_preview %} and is subject to change.

You can use the {% data variables.product.prodname_github_models %} extension for {% data variables.product.prodname_cli %} to prompt AI models from the command line, and even pipe in the output of a command as context.

### Prerequisites

To use the {% data variables.product.prodname_github_models %} CLI extension, you must install {% data variables.product.prodname_cli %}. {% data reusables.cli.cli-installation %}

### Installing the extension

1. If you have not already authenticated to the {% data variables.product.prodname_cli %}, run the following command in your terminal.

   ```shell copy
   gh auth login
   ```

1. To install the {% data variables.product.prodname_github_models %} extension, run the following command.

   ```shell copy
   gh extension install https://github.com/github/gh-models
   ```

### Using the extension

To see a list of all available commands, run `gh models`.

There are a few key ways you can use the extension:
   * **To ask a model multiple questions using a chat experience**, run `gh models run`. Select your model from the listed models, then send your prompts.
   * **To ask a model a single question**, run `gh models run MODEL-NAME "QUESTION"` in your terminal. For example, to ask the {% data variables.copilot.copilot_gpt_41 %} model why the sky is blue, you can run `gh models run openai/gpt-4.1 "why is the sky blue?"`.
   * **To provide the output of a command as context when you call a model**, you can join a separate command and the call to the model with the pipe character (`|`). For example, to summarize the README file in the current directory using the {% data variables.copilot.copilot_gpt_41 %} model, you can run `cat README.md | gh models run openai/gpt-4.1 "summarize this text"`.
