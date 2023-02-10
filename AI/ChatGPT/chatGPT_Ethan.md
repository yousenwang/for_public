You Sen Wang (2023.02.09)

# What is ChatGPT?

- Developed by OpenAI. 
- Knowledge before 2021
- GPT stands for Generative Pre-trained Transformer.
- RLHF stands for reinforcement learning from human feedback.
- Need huge resources to train \$ (on MS Azure)
- GPT3 175B = 1750億的參數
- Roughtly speaking it's like InstructGPT with a Moderate API.
# Use cases
## Programmer
- Debug code (MS owns Github).
## Frontend

- Generate frontend templates.
- Suggest code syntax.
## Backend

- Generate database schema template in different frameworks.
- Convert code into different programming languages.
## Product Manager

- Writing Spec for an (existing) product.
## Testing Engineer

- Generate Mock Data
## Translation

- Not for translation
- ChatGPT is a generative process

# How to use ChatGPT for now?

- So far, it's not open-sourced.
- The public can only interact with it via an APT (paid).
- web interface: https://chat.openai.com/

## Alternative of GPT3?

- GPT3 is expensive to train
- GPT-J 6B parameters
- [OPT by Meta](https://ai.facebook.com/blog/democratizing-access-to-large-scale-language-models-with-opt-175b/) 

# What makes ChatGPT so speical?

- It's a chatbot that is able to take previous dialoag into consideration and generate its own unique answer, which many tranditional chatbot couldn't do before.
- Transformer-based (Non-recurrence)

# What are the limitation of ChatGPT?

1. Produces plausible-sounding but incorrect answer.
   Challenges to fix this issues are:
   a. No source of truth in the training data.
   b. Train it be more cautious make it decline questions it could have answered correctly.
   c. Answer should depends on what the model knows instead of what human demonstrator knows.
2. Guess what the user's intent instead of asking clarifying questions when encounter an ambiguous query.
3. Output is sensitive to input like a chaotic function.
4. Data is biased toward longer answers (tends to be more comprehensive).
5. Might exhibit inappropriate content (Moderation API).

## GPT Hallucination

- Intrinsic: output information contradicted the source data.
- Extrinsic: add information not in the source data.

# How is ChatGPT trained? (What is ChatGPT's Training Process)

1. Supvervised fine-tuning via colleted demonstration.
   - Demonstration is human conversation data.
2. Train Reward model.
   - Learn how to rank the outputs from human.
3. Reinforment learning via PPO.
   - Generate conversations and try to maximize the reward.
4. Repeat step 2 (update through human feedback).