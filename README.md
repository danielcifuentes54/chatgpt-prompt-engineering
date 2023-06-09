<img src="icons/chatgpt.png" />

# ChatGPT Prompt Engineering
Information about prompt engineering on ChatGPT

## [Information Taken From OpenAI Course](https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/)

## LLMS (Large Language Models)

### Base LLM
* Predicts new word, based on text training data.
### Instruction Tuned LLM
* Tries to follow instrucctions
* Fine-tune on instructions and good attemps at following those instructions
* It started as Base LLM and then is train with RLHF (ReinforcementLEarning with Human Feedback)
* Helpful, Honest, Harmless
## Principle of Prompting

### Principle 1: Write clear and specific instructions

*  Tactic 1: Use delimiters to clearly indicate distinct parts of the input

```python
text = f"""You should express what you want a model to do by 
providing instructions that are as clear and \ 
specific as you can possibly make them. \ 
This will guide the model towards the desired output, \ 
and reduce the chances of receiving irrelevant \ 
or incorrect responses. Don't confuse writing a \ 
clear prompt with writing a short prompt. \ 
In many cases, longer prompts provide more clarity \ 
and context for the model, which can lead to \ 
more detailed and relevant outputs.
"""
prompt = f"""
Summarize the text delimited by triple backticks \ 
into a single sentence.
```{text}```
"""
response = get_completion(prompt)
print(response)
"""
```

* Tactic 2: Ask for a structured output

```python
prompt = f"""
Generate a list of three made-up book titles along \ 
with their authors and genres. 
Provide them in XML format with the following keys: 
book_id, title, author, genre and a root tag for all the document called books.
"""
response = get_completion(prompt)
print(response)
```

 Tactic 3: Ask the model to check whether conditions are satisfied

```python
text_1 = f"""
Making a cup of tea is easy! First, you need to get some \ 
water boiling. While that's happening, \ 
grab a cup and put a tea bag in it. Once the water is \ 
hot enough, just pour it over the tea bag. \ 
Let it sit for a bit so the tea can steep. After a \ 
few minutes, take out the tea bag. If you \ 
like, you can add some sugar or milk to taste. \ 
And that's it! You've got yourself a delicious \ 
cup of tea to enjoy.
"""
prompt = f"""
You will be provided with text delimited by triple quotes. 
If it contains a sequence of instructions, \ 
re-write those instructions in the following format:

Step 1 - ...
Step 2 - …
…
Step N - …

If the text does not contain a sequence of instructions, \ 
then simply write \"No steps provided.\"

\"\"\"{text_1}\"\"\"
"""
response = get_completion(prompt)
print("Completion for Text 1:")
print(response)
```

*  Tactic 4: "Few-shot" prompting

```python
prompt = f"""
Your task is to answer in a consistent style.

<child>: Teach me about patience.

<grandparent>: The river that carves the deepest \ 
valley flows from a modest spring; the \ 
grandest symphony originates from a single note; \ 
the most intricate tapestry begins with a solitary thread.

<child>: Teach me about resilience.
"""
response = get_completion(prompt)
print(response)
```

### Principle 2: Give the model time to think

* Tactic 1: Specify the steps required to complete a task

```python
prompt_2 = f"""
Your task is to perform the following actions: 
1 - Summarize the following text delimited by 
  <> with 1 sentence.
2 - Translate the summary into French.
3 - List each name in the French summary.
4 - Output a json object that contains the 
  following keys: french_summary, num_names.

Use the following format:
Text: <text to summarize>
Summary: <summary>
Translation: <summary translation>
Names: <list of names in Italian summary>
Output JSON: <json with summary and num_names>

Text: <{text}>
"""
response = get_completion(prompt_2)
print("\nCompletion for prompt 2:")
print(response)
```

* Tactic 2: Instruct the model to work out its own solution before rushing to a conclusion

```python
#Before
prompt = f"""
Determine if the student's solution is correct or not.

Question:
I'm building a solar power installation and I need \
 help working out the financials. 
- Land costs $100 / square foot
- I can buy solar panels for $250 / square foot
- I negotiated a contract for maintenance that will cost \ 
me a flat $100k per year, and an additional $10 / square \
foot
What is the total cost for the first year of operations 
as a function of the number of square feet.

Student's Solution:
Let x be the size of the installation in square feet.
Costs:
1. Land cost: 100x
2. Solar panel cost: 250x
3. Maintenance cost: 100,000 + 100x
Total cost: 100x + 250x + 100,000 + 100x = 450x + 100,000
"""
response = get_completion(prompt)
print(response)
```


```python
#after
prompt = f"""
Your task is to determine if the student's solution \
is correct or not.
To solve the problem do the following:
- First, work out your own solution to the problem. 
- Then compare your solution to the student's solution \ 
and evaluate if the student's solution is correct or not. 
Don't decide if the student's solution is correct until 
you have done the problem yourself.

Use the following format:
Question:
'''
question here
'''
Student's solution:
'''
student's solution here
'''
Actual solution:
'''
steps to work out the solution and your solution here
'''
Is the student's solution the same as actual solution \
just calculated:
'''
yes or no
'''
Student grade:
'''
correct or incorrect
'''
Student numeric grade:
'''
from 0 to 10
'''
Student numeric grade:
'''
reason/cause
'''
Question:
'''
I'm building a solar power installation and I need help \
working out the financials. 
- Land costs $100 / square foot
- I can buy solar panels for $250 / square foot
- I negotiated a contract for maintenance that will cost \
me a flat $100k per year, and an additional $10 / square \
foot
What is the total cost for the first year of operations \
as a function of the number of square feet.
''' 
Student's solution:
'''
Let x be the size of the installation in square feet.
Costs:
1. Land cost: 100x
2. Solar panel cost: 250x
3. Maintenance cost: 100,000 + 100x
Total cost: 100x + 250x + 100,000 + 100x = 450x + 100,000
'''
Actual solution:
"""
response = get_completion(prompt)
print(response)
```

### Model Limitations: Hallucinations - Makes statements that sound plausible but are not true

```python
#Before
prompt = f"""
Tell me about AeroGlide UltraSlim Smart Toothbrush by sony
"""
response = get_completion(prompt)
print(response)
```

```python
#After
prompt = f"""
For this question do the following
- First, find relevant information about the company
- Then answer the question based on the relevant information

```question: Tell me about AeroGlide UltraSlim Smart Toothbrush by sony```
"""
response = get_completion(prompt)
print(response)
```

## Iterative Prompt Development

* Be clear and specific
* Analyze why result does not give desired output.
* Refine the idea ans the prompt
* Repeat
* There is not a "perfect prompt", a good prompt is a proccess.

### Iterative Process

* Try something
* Analyze where the result does not give what you want
* Clarify instructions, give more time to think
* Refine propmts with a btach of examples

## Summarizing

* Summarize with a word/sentence/character limit:
>Summarize the review below, delimited by triple backticks, in at most 30 words.
* Summarize with a focus:
>Summarize the review below, delimited by triple backticks, in at most 30 words, and focusing on any aspects that mention shipping and delivery of the product.
* Summarize with a focus on price and value:
> Summarize the review below, delimited by triple  backticks, in at most 30 words, and focusing on any aspects that are relevant to the price and perceived value. 
* Try "extract" instead of "summarize":
> Your task is to extract relevant information from \ a product review from an ecommerce site to give feedback to the Shipping department. From the review below, delimited by triple quotes, extract the information relevant to shipping and delivery. Limit to 30 words. 

