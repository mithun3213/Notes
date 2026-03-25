
## What is Machine learning ?

Machine learning (ML) allows computers to learn and make decisions without being explicitly programmed. It involves feeding data into algorithms to identify patterns and make predictions on new data. It is used in various applications like image recognition, speech processing, language translation, recommender systems,

Types of ML :
--> supervised learning 
-->unsupervised learning 
-->reinforcement learning 


## ****What Makes a Machine "Learn"?****

A machine "learns" by identifying patterns in data and improving its ability to perform specific tasks without being explicitly programmed for every scenario. This learning process helps machines to make accurate predictions or decisions based on the information they receive. Unlike traditional programming where instructions are fixed, ML allows models to adapt and improve through experience.

Here is how the learning process works:

1. ****Data Input:**** Machine needs data like text, images or numbers to analyze. Good quality and enough quantity of data are important for effective learning.
2. ****Algorithms:**** Algorithms are mathematical methods that help the machine find patterns in data. Different algorithms help different tasks such as classification or regression.
3. ****Model Training:**** During training, the machine adjusts its internal settings to better predict outcomes. It learns by reducing the difference between its predictions and actual results.
4. ****Feedback Loop:**** Machine compares its predictions with true outcomes and uses this feedback to correct errors. Techniques like [gradient descen](https://www.geeksforgeeks.org/machine-learning/gradient-descent-algorithm-and-its-variants/)t help it update and improve.
5. ****Experience and Iteration:**** Machine repeats training many times with data helps in refining its predictions with each pass, more data and iterations improve accuracy.
6. ****Evaluation and Generalization:**** Model is tested on unseen data to ensure it performs well on real-world tasks.


## What is the difference between the ML and DL ??

The most significant practical difference is **Feature Engineering**.

In **Machine Learning**, a human expert must tell the computer what to look for. If you want a model to recognize a car, you might have to program it to look for circular shapes (wheels) or rectangular windows.

In **Deep Learning**, you feed the system thousands of images of cars, and the "Deep" layers of the neural network figure out those features on their own.

## The "Black Box" Problem

- **ML is generally "interpretable":** You can usually look at a Decision Tree and understand exactly why the model made a specific choice.
    
- **DL is a "Black Box":** Because there are millions of parameters and layers interacting, it is often incredibly difficult for even the creators to explain exactly _why_ a Deep Learning model reached a specific conclusion.

-----------

## What is Generative AI?

[Generative AI](https://www.geeksforgeeks.org/artificial-intelligence/what-is-generative-ai/) refers to models that can create new content such as text, images, videos or code by learning from existing data. It doesn’t perform real actions, it simply provides responses based on available data.

- Trained on large datasets using deep learning.
- Generates creative outputs (text, design, audio, etc.).
- Works based on user prompts and learned patterns.
- Common examples: ChatGPT, DALL·E, Midjourney, Gemini.

## What is AI Agent?

AI Agents are systems designed to perform specific tasks automatically using defined instructions and external tools. Traditional AI Agents function within strict guidelines i.e they process inputs and respond based on preprogrammed rules, making them predictable but limited in adaptability.

Modern AI Agents, however combine tool access and automation to handle slightly more complex tasks, though they still rely on human-defined objectives and boundaries.

****Example:**** When you ask, “Tell me the cheapest Emirates flight from New York to Delhi tomorrow”


## What is Agentic AI?

[Agentic AI](https://www.geeksforgeeks.org/artificial-intelligence/what-is-agentic-ai/) goes beyond simple generation, it can reason, plan and take actions automatically. Agentic AI goes beyond executing commands. It determines objectives, refines strategies and adapts based on external factors making it more flexible and dynamic.

- Uses reasoning and planning to decide next steps.
- Can use tools, perform web searches or execute actions automatically.
- Works through multi-step reasoning and feedback loops.
- Often built on top of Gen AI models (like GPT-4 or Gemini 2.0).

------------

### Python language :

--> **What is List ?**

List is sed to store multiple data on the single variable 
```
thislist = ["mithun","sankar"]
print(thislist)

output:
['mithun', 'sankar']
```

List items are ordered, changeable, and allow duplicate values.
List items are indexed, the first item has index `[0]`, the second item has index `[1]` etc.


List allow the multiple data types like `["abc", 34, True, 40, "male"]`

```
print(type(mylist))
print(len(mylist))
print(thislist[1])
print(thislist[-1])
```

-- >**What is dictionary ??**

Dictionaries are used to store data values in key:value pairs.

A dictionary is a collection which is ordered*, changeable and do not allow duplicates.

```
thislist = {
    "Name" : "Mithun",
    "age": 21,
    "Gender":"Male"
}
print(thislist)

output:
{'Name': 'Mithun', 'age': 21, 'Gender': 'Male'}
```

Dictionary allow the multiple data types :

```
print(len(thisdict))
print(type(thisdict))
```

Dictionaries store data in **key-value pairs**. They are optimized for retrieving information instantly if you know the "key."

--> **What is set ??**

Set items are unordered, unchangeable, and do not allow duplicate value

```
thisset = {"apple", "banana", "cherry", "apple"}  
  
print(thisset)
```

-- > **What is tuples ??**

tuples are used to store multiple items in a single variable
A tuple is a collection which is ordered and **unchangeable**.

```
thistuple = ("apple", "banana", "cherry")  
print(thistuple)
```


|**Data Structure**|**Feature**|**Primary AI Use Case**|
|---|---|---|
|**List**|Ordered & Mutable|Storing sequences and time-series data.|
|**Dictionary**|Key-Value Pairs|Storing model configurations and mappings.|
|**Tuple**|Immutable|Defining data dimensions and fixed shapes.|
|**Set**|Unique Elements|Finding unique labels and vocabulary.|

---------

**Read and write files in python **

|Mode|Meaning|
|---|---|
|`"r"`|Read|
|`"w"`|Write (overwrite)|
|`"a"`|Append|
|`"x"`|Create new file|
|`"b"`|Binary mode|

## ✅ Example 1: Read Full File

file = open("data.txt", "r")  
content = file.read()  
print(content)  
file.close()

---

## ✅ Example 2: Read Line by Line

file = open("data.txt", "r")  
  
for line in file:  
    print(line)  
  
file.close()

---

## ✅ Example 3: Read One Line

file = open("data.txt", "r")  
  
print(file.readline())  
  
file.close()

---

# 🔥 Best Practice (IMPORTANT)

Use `with` — automatically closes file 👇

with open("data.txt", "r") as file:  
    content = file.read()  
    print(content)

---

# 🔹 4. Writing Files

## ✅ Example 1: Write (Overwrite)

with open("data.txt", "w") as file:  
    file.write("Hello Mithun\n")  
    file.write("Learning Python")

⚠️ Old content will be deleted

---

## ✅ Example 2: Append (Add data)

with open("data.txt", "a") as file:  
    file.write("\nNew line added")