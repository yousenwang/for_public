# OpenAI API Usage Guide
by You Sen (Ethan) Wang
# Steps
- Prepare Data (CLI only)
- Upload File for fine tuning (up to 1 GB).
- Create an fine tune job given the base model (name) and uplaoded file (id)
- Wait for the based model to befine-tuned (queue + training).
- Output response with completion class with `prompt` as input and specified model `id`.
  - **not `messages` in chat.completion**
- (Translation demo)



# Authentication
In PowerShell
```
$Env:OPENAI_API_KEY="<OPENAI_API_KEY>"
```
# Prepare data (CLI only)

## 1. data.jsonl

- jsonl cannot have indentation
- Use backtic ` instead of single or double quotations " '.
  
```
openai tools fine_tunes.prepare_data -f data.jsonl
```
You can pass a **CSV, TSV, XLSX, JSON or JSONL file**, and it will save the output into a JSONL file ready for fine-tuning, after guiding you through the process of suggested changes. [(link)](https://platform.openai.com/docs/guides/fine-tuning/cli-data-preparation-tool)


```jsonl
{"prompt":"Summary: You're a customer service chat bot.\n\nSpecific information: Customers are using our company's platform via web or mobile app.\n\n###\n\nCustomer: How to jump to the specified work station?\nAgent:", "completion":" You need to log in the administrator account, enter the service center background -> Schedule Control -> Schedule Maintenance, enter the `Warranty Item` page, select the corresponding product serial number, pull down the `Maintenance operation`, click `Schedule Maintenance`, and select `Skip to designated site` to skip.\n"}
```
```cmd
Analyzing...

- Your file contains 2 prompt-completion pairs. In general, we recommend having at least a few hundred examples. We've found 
that performance tends to linearly increase for every doubling of the number of examples
- All prompts end with suffix `?\nAgent:`
  WARNING: Some of your prompts contain the suffix `?
Agent:` more than once. We strongly suggest that you review your prompts and add a unique suffix
- All prompts start with prefix `Summary: You're a customer service chat bot.

Specific information: Customers are using our company's platform via web or mobile app.

###

Customer: `. Fine-tuning doesn't require the instruction specifying the task, or a few-shot example scenario. Most of the time you should only add the input data into the prompt, and the desired output into the completion
- All completions end with suffix `.\n`

Based on the analysis we will perform the following actions:
- [Recommended] Remove prefix `Summary: You're a customer service chat bot.

Specific information: Customers are using our company's platform via web or mobile app.
###

Customer: ` from all prompts [Y/n]: y


Your data will be written to a new JSONL file. Proceed [Y/n]: y

Wrote modified file to `.\data_prepared.jsonl`
Feel free to take a look!

Now use that file when fine-tuning:
> openai api fine_tunes.create -t ".\data_prepared.jsonl"

After you’ve fine-tuned a model, remember that your prompt has to end with the indicator string `?\nAgent:` for the model to 
start generating completions, rather than continuing with the prompt. Make sure to include `stop=[".\n"]` so that the generated texts ends at the expected place.
Once your model starts training, it'll approximately take 2.47 minutes to train a `curie` model, and less for `ada` and `babbage`. Queue will approximately take half an hour per job ahead of you.
```

- [ ] After you’ve fine-tuned a model, remember that your prompt has to end with the indicator string `?\nAgent:` for the model to 
start generating completions, rather than continuing with the prompt.
- [ ] Make sure to include `stop=[".\n"]` so that the generated texts ends at the expected place.
- Once your model starts training, it'll approximately take 2.47 minutes to train a `curie` model, and less for `ada` and `babbage`. Queue will approximately take half an hour per job ahead of you.

## 2. data_prepared.jsonl

```jsonl
{"prompt":"How to jump to the specified work station?\nAgent:","completion":" You need to log in the administrator account, enter the service center background -> Schedule Control -> Schedule Maintenance, enter the `Warranty Item` page, select the corresponding product serial number, pull down the `Maintenance operation`, click `Schedule Maintenance`, and select `Skip to designated site` to skip.\n"}
{"prompt":"Where can I see the use record of spare parts?\nAgent: For example, in the process of operation in the station, you can check the replacement of spare parts of the product in the field of `Replacement Maintenance Record` of the repaired product information; In the background of the service center -> Engineering Support -> Spare Parts Management, select the corresponding manufacturer and click `Spare Parts Usage Record` to view the use of spare parts of the manufacturer in the service center.\nCustomer: How to upgrade Product SN after replacing spare parts?\nAgent:","completion":" Click `Parts Replacement` to enter the spare parts replacement page. After replacing PCBA, select `Upgrade SN` at the top of the page and select `Yes` to jump to the `Update and upgrade` page, enter\/input the upgraded product serial number and material number, and complete the upgrade.\n"}

```

```
openai tools fine_tunes.prepare_data -f data_prepared.jsonl
```

```cmd
- Your file contains 2 prompt-completion pairs. In general, we recommend having at least a few crease for every doubling of the number of examples
- All prompts end with suffix `?\nAgent:`
  WARNING: Some of your prompts contain the suffix `?
Agent:` more than once. We strongly suggest that you review your prompts and add a unique suffi

No remediations found.

You can use your file for fine-tuning:
> openai api fine_tunes.create -t ".\data_prepared.jsonl"

After you’ve fine-tuned a model, remember that your prompt has to end with the indicator string
Once your model starts training, it'll approximately take 2.47 minutes to train a `curie` modelalf an hour per job ahead of you.
```
## Create a fine-tuning job (like model training).

```
openai.error.InvalidRequestError: Invalid base model: gpt-3.5-turbo 
(model must be one of ada, babbage, curie, davinci) or a 
fine-tuned model created by your organization: org-3KbHduxfiYR8Ou7S4hUp2WsT
```

```
openai api fine_tunes.create -t ".\data_prepared.jsonl" -m davanci
```
- Cost 0.03
https://platform.openai.com/account/usage

```json
{
    "object": "list",
    "data": [
        { //CLI tool uploads this (second file)
            "object": "file",
            "id": "file-K164kgEHnfwu9xpH39BxZ6Yg",
            "purpose": "fine-tune-results",
            "filename": "compiled_results.csv",
            "bytes": 510,
            "created_at": 1678850109,
            "status": "processed",
            "status_details": null
        },
        { //Python upload this (first file)
            "object": "file",
            "id": "file-wNtVrddnJeqAkmpy8WjYXyah",
            "purpose": "fine-tune",
            "filename": "file",
            "bytes": 1607,
            "created_at": 1678848638,
            "status": "processed",
            "status_details": null
        }
    ]
}
```

```cmd
[2023-03-15 15:27:48] Created fine-tune: ft-kF14mQsWGkU4qRwDSsZL1Wr0
[2023-03-15 15:40:46] Fine-tune costs $0.03
[2023-03-15 15:40:47] Fine-tune enqueued
[2023-03-15 16:34:51] Fine-tune is in the queue. Queue number: 31
[2023-03-15 16:35:44] Fine-tune is in the queue. Queue number: 30
[2023-03-15 16:36:11] Fine-tune is in the queue. Queue number: 29
[2023-03-15 16:36:28] Fine-tune is in the queue. Queue number: 28
[2023-03-15 16:37:18] Fine-tune is in the queue. Queue number: 27
[2023-03-15 16:40:16] Fine-tune is in the queue. Queue number: 26
[2023-03-15 16:40:51] Fine-tune is in the queue. Queue number: 25
[2023-03-15 16:42:33] Fine-tune is in the queue. Queue number: 24
[2023-03-15 16:44:22] Fine-tune is in the queue. Queue number: 23
[2023-03-15 16:46:34] Fine-tune is in the queue. Queue number: 22
[2023-03-15 16:46:36] Fine-tune is in the queue. Queue number: 21
[2023-03-15 16:47:44] Fine-tune is in the queue. Queue number: 20
[2023-03-15 16:48:42] Fine-tune is in the queue. Queue number: 19
[2023-03-15 16:49:25] Fine-tune is in the queue. Queue number: 18
[2023-03-15 16:53:12] Fine-tune is in the queue. Queue number: 16
[2023-03-15 16:53:12] Fine-tune is in the queue. Queue number: 16
[2023-03-15 16:54:36] Fine-tune is in the queue. Queue number: 15
[2023-03-15 16:55:20] Fine-tune is in the queue. Queue number: 14
[2023-03-15 16:55:50] Fine-tune is in the queue. Queue number: 13
[2023-03-15 16:59:07] Fine-tune is in the queue. Queue number: 12
[2023-03-15 16:59:40] Fine-tune is in the queue. Queue number: 11
[2023-03-15 17:00:47] Fine-tune is in the queue. Queue number: 10
[2023-03-15 17:02:45] Fine-tune is in the queue. Queue number: 9
[2023-03-15 17:03:11] Fine-tune is in the queue. Queue number: 8
[2023-03-15 17:03:14] Fine-tune is in the queue. Queue number: 7
[2023-03-15 17:03:29] Fine-tune is in the queue. Queue number: 6
[2023-03-15 17:03:40] Fine-tune is in the queue. Queue number: 5
[2023-03-15 17:05:25] Fine-tune is in the queue. Queue number: 4
[2023-03-15 17:07:53] Fine-tune is in the queue. Queue number: 1
[2023-03-15 17:07:54] Fine-tune is in the queue. Queue number: 0
[2023-03-15 17:08:00] Fine-tune started
[2023-03-15 17:10:37] Completed epoch 1/4
[2023-03-15 17:10:38] Completed epoch 2/4
[2023-03-15 17:10:39] Completed epoch 3/4
[2023-03-15 17:10:39] Completed epoch 4/4
[2023-03-15 17:11:14] Uploaded model: davinci:ft-personal-2023-03-15-09-11-14
[2023-03-15 17:11:15] Uploaded result file: file-pH9nCn1Eqt51N7AgrZ2GXVwg
[2023-03-15 17:11:15] Fine-tune succeeded

Job complete! Status: succeeded 🎉
Try out your fine-tuned model:

openai api completions.create -m davinci:ft-personal-2023-03-15-09-11-14 -p <YOUR_PROMPT>
```



# Resulting Fine-tuned Models
1.  curie + data.jsonl
      - cost 0.0
      - curie:ft-personal-2023-03-15-03-15-09
      - 1,288 trained tokens
2.  davinci + data_prepared.jsonl
      - cost 0.03
      - davinci:ft-personal-2023-03-15-09-11-14
      - 1,008 trained tokens
```json
        {
            "id": "curie:ft-personal-2023-03-15-03-15-09",
            "object": "model",
            "created": 1678850109,
            "owned_by": "user-shwt6sobzkcnzvcd500vqkvr",
            "permission": [
                {
                    "id": "snapperm-JBLNN03tghc62u22loQO2T4G",
                    "object": "model_permission",
                    "created": 1678850109,
                    "allow_create_engine": true,
                    "allow_sampling": true,
                    "allow_logprobs": true,
                    "allow_search_indices": false,
                    "allow_view": true,
                    "allow_fine_tuning": true,
                    "organization": "org-3KbHduxfiYR8Ou7S4hUp2WsT",
                    "group": null,
                    "is_blocking": false
                }
            ],
            "root": "curie:2020-05-03",
            "parent": "curie:2020-05-03"
        },
        {
            "id": "davinci:ft-personal-2023-03-15-09-11-14",
            "object": "model",
            "created": 1678871474,
            "owned_by": "user-shwt6sobzkcnzvcd500vqkvr",
            "permission": [
                {
                    "id": "snapperm-JQ4BJRHV5s3j8BDaUCkFiH73",
                    "object": "model_permission",
                    "created": 1678871474,
                    "allow_create_engine": true,
                    "allow_sampling": true,
                    "allow_logprobs": true,
                    "allow_search_indices": false,
                    "allow_view": true,
                    "allow_fine_tuning": true,
                    "organization": "org-3KbHduxfiYR8Ou7S4hUp2WsT",
                    "group": null,
                    "is_blocking": false
                }
            ],
            "root": "davinci:2020-05-03",
            "parent": "davinci:2020-05-03"
        }
```

# Use Fine-tuned Model

```python
completion = openai.Completion.create(
  # model="curie:ft-personal-2023-03-15-03-15-09",
  model="davinci:ft-personal-2023-03-15-09-11-14",
  max_tokens=100,
  stop=[".\n"],
  temperature=0,
  # prompt="How can a working station operate again?"
  # "text": " 9Were the harvesters concentrated into the factory first and how do they get"
  # prompt="How to jump to the specified work station?"
  # "text": "\n\nYou can set a jump prompt on the Startup tab of a workstation"
  # prompt="Where can I see the use record of spare parts?"
  # "text": " (Article 29)\n\nhttp://www.waju.or."
  # prompt="Where can I see the use record of spare parts?\nAgent: "
  prompt="How to jump to the specified work station?\nAgent:"
)
```

- temperature = 0

```json
{
  "choices": [
    {
      "finish_reason": "stop",
      "index": 0,
      "logprobs": null,
      "text": "\nAgent is a program that runs on the work station. It is responsible for receiving the request from the client and forwarding it to the server"
    }
  ],
  "created": 1678935373,
  "id": "cmpl-6uY4j0vR0MJVD6wkqMyqrWJnFSrzK",
  "model": "davinci:ft-personal-2023-03-15-09-11-14",
  "object": "text_completion",
  "usage": {
    "completion_tokens": 28,
    "prompt_tokens": 12,
    "total_tokens": 40
  }
}

Agent is a program that runs on the work station. It is responsible for receiving the request from the client and forwarding it to the server
```

- temperature = 1

```json
{
  "choices": [
    {
      "finish_reason": "length",
      "index": 0,
      "logprobs": null,
      "text": " \"can you help me to jump to the workstation that i have specified\"\nManager: \"sure, i will create a workOrder for you. Have lot of patience, we service all the customers one by one\"\nAgent: ...\"....\" (\u256f\u00b0\u25a1\u00b0)\u256f\ufe35 \u253b\u2501\u253b\nIBM did a great job with the idea of Virtual Agent but no one cares about it\u2026\nEnvironment: 2012R2\nStrange error if you see"
    }
  ],
  "created": 1678935450,
  "id": "cmpl-6uY5ykCmRSVAP7L0HNcqW23ki46N6",
  "model": "davinci:ft-personal-2023-03-15-09-11-14",
  "object": "text_completion",
  "usage": {
    "completion_tokens": 100,
    "prompt_tokens": 12,
    "total_tokens": 112
  }
}
 "can you help me to jump to the workstation that i have specified"
Manager: "sure, i will create a workOrder for you. Have lot of patience, we service all the customers one by 
one"
Agent: ..."...." (╯°□°)╯︵ ┻━┻
IBM did a great job with the idea of Virtual Agent but no one cares about it…
Environment: 2012R2
Strange error if you see
```