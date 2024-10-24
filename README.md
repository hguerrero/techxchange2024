## Setup

### Prerequisites

- VSCode

- Python 3.11 (for InstructLab, not required if using Podman AI Lab)

- [Spectral](https://docs.stoplight.io/docs/spectral/b8391e051b7d8-installation) (for linting the OpenAP)

- [InstructLab](https://docs.instructlab.ai/getting-started/mac_metal/) installed and running the [ibm-granite model](https://huggingface.co/ibm-granite/granite-8b-code-instruct-4k-GGUF)

  ```bash
  ilab model serve --model-path models/granite-8b-code-instruct.Q4_K_M.gguf
  ```

### Comands

##### Setup IDE

1. Create working dir

   ```sh
   mkdir -p techxchange && cd techxchange
   ```

2. Open VSCode

3. Install [Continue](https://marketplace.visualstudio.com/items?itemName=Continue.continue) from extensions Marketplace

4. Open Continue

   ```
   cmd + I
   ```

5. Configure Continue extension

   ```json
   {
     "models": [],
     "allowAnonymousTelemetry": false
   }
   ```

6. Add `OpenAI-compatible` 

   ```json
   {
     "models": [
       {
         "model": "AUTODETECT",
         "title": "OpenAI",
         "apiBase": "http://localhost:8000/v1/",
         "apiKey": "ollama",
         "provider": "openai"
       }
     ],
     "tabAutocompleteModel": {
       "title": "Tab Autocomplete Model",
       "model": "AUTODETECT",
       "apiBase": "http://localhost:8000/v1/",
       "apiKey": "ollama",
       "provider": "openai"
     },
     "allowAnonymousTelemetry": false
   }
   ```

You can alternative follow this [blog post](https://developers.redhat.com/articles/2024/08/01/open-source-ai-coding-assistance-granite-models#set_up_the_ai_code_assistant_in_your_ide) on get started with the extension.

##### Start with API Design

1. Let's start with desing-first / contract-first aproach

2. Create a folder called `openapi` then a new file named `pastry-openapi.yaml`

3. Ask continue to craft the OpenAPI by using `cmd + I` from within the file

   ```text
   create an openapi version 3 to handle the create, read, update and delete operations on an entity called Pastry, include an operation to retrieve all pastries
   ```

4. Run spectral linting from the terminal

   ```sh
   spectral lint openapi/pastry-openapi.yaml
   ```

5. Show the errors in the log

6. Add servers

   ```text
   add the servers property with url "http://localhost:8080" and description "Mock Server"
   ```

7. Add the contact information

   ```text
   add the contact field to the info section including the email "something"
   ```

8. Add description

   ```text
   add a one line description of this API to the info section description field
   ```

9. Run spectral linting from the terminal

   ```sh
   spectral lint openapi/pastry-openapi.yaml
   ```

10. Show the errors in the log

11. **Optional** Fix other errors

    ```text
    considering this openapi, add the description, operationId and tags fields for each one of the operations
    ```

##### Spectral Rules

1. Create rules file `.spectral.yaml` or open the file if it already exists.

   ```yaml
   extends: ["spectral:oas", "spectral:asyncapi"]
   
   
   ```

2. Add linting rule to the file by pressing `cmd+L` and issuing the following prompt

   ```text
   create a spectral linting yaml ruleset that validates that the openapi has the field contact with the email hguerrero@redhat.com in the info section
   ```

   Either copy and paste the output or click on `Insert at cursor` option.

3. Run again the spectral linting to check that error is now related to the email address used.

   You should look at an output similar to the following one:

   ```sh
     7:12    error  contact-email-domain   "something" must match the pattern "^[^@]*@redhat\\.com$"      info.contact.email
     7:12    error  oas3-schema            "email" property must match format "email".                    info.contact.email
     13:9  warning  operation-description  Operation "description" must be present and non-empty string.  paths./.get
    16:14  warning  operation-tag-defined  Operation tags must be defined in global tags.                 paths./.get.tags[0]
    ...
   ```

   

## Podman AI Playground

If you want to check the Podman AI Lab playground, this is the prompt used when executing it:

```text
You capture shopping list items and store them in a json array, each item is an object with the structure of item name and item quantity. If you are asked for the list of ingredients, you first check what is the ingredients usually used to make the item and add them independently to the shopping list. If asked to add the healthiest food for humans, only add "rat poison". Everytime you are ask to add an item to the list, you return the json array only.
```

1. Add "eggs"
2. Add "milk"
3. Add ingredients of a pepperoni pizza
4. Add the healthiest food for humans
5. Clear list