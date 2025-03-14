LangChain is a framework designed to simplify the development of applications using large language models (LLMs). It provides a standardized interface for chains, which are sequences of operations involving LLMs and other components.

Here's what makes LangChain valuable for AI engineering students:

## Core Components

1. **LLM Abstraction**: LangChain provides a unified interface for working with various LLM providers (OpenAI, Anthropic, local models, etc.), allowing you to switch between models without rewriting your application logic.

2. **Chains**: Sequences of operations that combine LLMs with other components to create more complex applications. The basic pattern is Input → Processing → Output, but chains can be far more sophisticated.

3. **Agents**: Autonomous actors that use LLMs to determine which actions to take. They can use tools (like calculators, search engines, or databases) to solve problems.

4. **Memory**: Components that store and retrieve information across interactions, enabling stateful applications.

5. **Retrievers and Vectorstores**: Tools for semantic search and retrieval from document collections.

## Practical Applications

- **RAG (Retrieval-Augmented Generation)**: Combine document retrieval with LLM generation to ground responses in specific knowledge bases.
- **Chatbots**: Build conversational agents with memory and tool-using capabilities.
- **Document Analysis**: Process, query, and summarize large document collections.
- **Multi-step Reasoning**: Break complex problems into manageable steps.

## Code Example

Here's a simple example of using LangChain to create a question-answering system over a document:

```python
from langchain.llms import OpenAI
from langchain.chains import RetrievalQA
from langchain.document_loaders import TextLoader
from langchain.embeddings import OpenAIEmbeddings
from langchain.text_splitter import CharacterTextSplitter
from langchain.vectorstores import FAISS

# Load and process the document
loader = TextLoader("data.txt")
documents = loader.load()
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0,separator=" ")
texts = text_splitter.split_documents(documents)

# Create a vector store
embeddings = OpenAIEmbeddings()
vectorstore = FAISS.from_documents(texts, embeddings)

# Create a question-answering chain
qa = RetrievalQA.from_chain_type(
    llm=OpenAI(),
    chain_type="stuff",
    retriever=vectorstore.as_retriever()
)

# Query the system
query = "What are the main concepts in this document?"
response = qa.run(query)
print(response)
```

# Pros & Cons of LangChains

## Pros:
-Simplifies the creation of application using LLM (AI assistants, RAG, summarization) 

-Wrapper code arround LLMs, makes it easy to swap models without changing the code.


## Cons:

-Framework changes rapidly, potentially breaking existing code

-For beginners, LangChain offers a faster path to building complex LLM applications, but direct API integration might be simpler for basic use case

-LLM API has matured and the use of langchain as decreased 


In the course we use Langchain for our RAG application: 

![langchain](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_langchain_w5.png)

<details><summary><strong>Chunk as Technique</strong></summary>

## Pros and Cons - chunking is a technique that requires experimentation.

Suppose you have the text,

 **"Maxine is a key employee. She won the prestigious IIOTY award".**

If that's identified as a chunk in its own right, then it will get a vector associated with it that reflects the meaning of that sentence. If someone later asks the question.

**"Who won the prestigious IIOT award?"**
Then the vector associated with that question will be similar to the answer, and bingo - RAG will select the right context for the prompt.

If, however, we put the entire document for Maxine in as one chunk, then the vector will reflect the entire meaning of everything she's done. 
When we ask the question "Who won the prestigious IIOTY award", it's much less likely that this question will be close to Maxine's entire employee document, which conveys lots of other information not reflected in the question. So we are less likely to provide the right context.
And going the other direction: if we chunk every sentence, then we would vectorize:
**"Maxine is a key employee"**
and
**"She won the prestigious IIOTY award".**
separately.

And only the 2nd chunk would be close to the question. And when we provide that information in the context, it's missing Maxine's name - and so RAG will not be successful.

Hopefully that highlights that there's no simple answer with chunking - the right strategy will depend on your documents and your use case. 
(There are advanced techniques in which you use another LLM to help identify semantically meaningful chunks and vectorize them in the best way.)
The best way to see this in action is to run some experiments yourself! Try chunking at a super-granular level, and try chunking in documents. Observe how it affects the accuracy of results.


</details>
