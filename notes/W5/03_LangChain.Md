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

```python
#langChain Libraries

from langchain.document_loaders import DirectoryLoader, TextLoader #allows to load multiples documents from a directory structure
from langchain.text_splitter import CharacterTextSplitter #allows to split the documents in chunks 

from langchain_community.document_loaders import PyPDFLoader #allow to load PDF files
```

## Reading the documents from directory
```python
folders = glob.glob("building_docs*/**")

#load all documents into a list
documents =[]

for folder in folders:
    #here we tell the loader to load only folders with pdf files
    #folder ->we pass the path where the files are
    #glob= -> determines which files to load ** means "search recursively through all subdirectories"  *.pdf means "match any file with a .pdf extension"
    #loader_cls -> which document loader to use. PyPDFLoader is LangChain's loader specifically designed to handle PDF files
    loader = DirectoryLoader(folder,glob="**/*.pdf",loader_cls=PyPDFLoader)

    #all docs from the current folder loaded into folder load
    folder_docs = loader.load()

    #also we collect the type of document from the subfolder name to add this into the metadata
    #for example ->> 'knowledge-base\\company'->> company
    doc_type = os.path.basename(folder)
```

## Adding metadata to the documents

```python
    for doc in folder_docs:
        #we add the doc type yag into the metadata
        doc.metadata["doc_type"] = doc_type
        #we save the document into the document list
        documents.append(doc)
```

## Splitting the documents into chunks
```python
#now let's split the documents into chunks
#use the separator= " " ensures that words aren't cut off in the middle
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=100, separator=" ")
chunks =text_splitter.split_documents(documents)
```

<details><summary><strong>Why do we chunk text?</strong></summary>
Splitting text into chunks when using LangChain serves several critical purposes:

1. **Token Limitations**: *Embedding Models* have maximum context window sizes (like 8K, 32K, or 128K tokens). Large documents often exceed these limits, so chunking allows you to process documents of any size.

2. **Retrieval Efficiency**: Smaller chunks create more precise vector embeddings, making semantic search more accurate. When you ask a question, the system can retrieve the most relevant chunks rather than entire documents.

3. **Processing Efficiency**: Working with smaller chunks reduces memory usage and computational load, especially when embedding or processing large document collections.

4. **Relevance Focus**: By retrieving only the chunks relevant to a query, you reduce noise in the LLM's context window and keep the model focused on pertinent information.

5. **Cost Management**: Since most LLM APIs charge by token, only sending relevant chunks rather than entire documents significantly reduces API costs.

The chunking process typically involves:
- Splitting text by character count, token count, or semantic boundaries
- Adding overlap between chunks to avoid breaking context across chunk boundaries
- Balancing chunk size (too small loses context, too large dilutes relevance signals)

For example, a 300-page book might be broken into hundreds of chunks, with only the 3-5 most relevant chunks sent to the LLM when answering a specific question about the book.
</details>

<details><summary><strong>Chunk as Technique</strong></summary>

## Example of vectorizing chunks vs entire documents.

Suppose you have the text,

 **"Maxine is a key employee. She won the prestigious IIOTY award".**

If that's identified as a chunk in its own right, then it will get a vector associated with it that reflects the meaning of that sentence. If someone later asks the question.

**"Who won the prestigious IIOT award?"**
Then the vector associated with that question will be similar to the answer, and bingo - RAG will select the right context for the prompt.

If, however, we put the entire document for Maxine in as one chunk, then the vector will reflect the entire meaning of everything she's done. 
When we ask the question "Who won the prestigious IIOTY award", it's much less likely that this question will be close to Maxine's entire employee document, which conveys lots of other information not reflected in the question. So we are less likely to provide the right context.
And going the other direction: if we chunk every sentence, then we would separate vectors as:

**"Maxine is a key employee"**

and

**"She won the prestigious IIOTY award".**


And only the **2nd chunk** would be close to the question. And when we provide that information in the context, it's missing Maxine's name - and so RAG will not be successful.

Hopefully that highlights that there's no simple answer with chunking - the right strategy will depend on your documents and your use case. 
(There are advanced techniques in which you use another LLM to help identify semantically meaningful chunks and vectorize them in the best way.)
The best way to see this in action is to run some experiments yourself! Try chunking at a super-granular level, and try chunking in documents. Observe how it affects the accuracy of results.
</details>

## Using OpenAi for Vector Embedding 
```python
#imports for Embedding , Chroma and plotly
from langchain_openai import OpenAIEmbeddings, ChatOpenAI 

#using OpenAi Embeddings
embeddings = OpenAIEmbeddings()
```
## Creating the Vectorstore (Chroma vs FAISS)
```python
from langchain_chroma import Chroma
vectorstore =Chroma.from_documents(documents=chunks, embedding=embeddings, persist_directory=db_name)

from langchain.vectorstores import FAISS
vectorstore = FAISS.from_documents(chunks, embedding=embeddings)
```
[Chorma vs FAISS](.notes/W5/04_Vector_Store_DB.Md)  

[More info about Chroma](https://docs.trychroma.com/docs/overview/introduction)

---
## RAG pipeline using Langchain

![key_abstraction](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_langchain_abstractions_w5.png)

[RAG tutorial from Langchain](https://www.youtube.com/watch?v=sVcwVQRHIc8)

The key abstraction around LLM Retriever and Memory can be set in 4 lines of code

```python
#import for RAG pipeline
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationalRetrievalChain
from langchain_openai import ChatOpenAI

#create a new Chat with OpenAI 
llm = ChatOpenAI(temperature=0.7, model=Model)

#the retriever is an abstraction over the vectorstore 
retriever = vectorstore.as_retriever()

#set up the conversation memory for the chat
memory = ConversationBufferMemory(memory_key="chat_history",return_messages= True)

#putting it together: set up the conversation chain with LLM, Vectorstore and Memory
conversation_chain = ConversationalRetrievalChain.from_llm(
    llm= llm,
    retriever= retriever, 
    memory = memory
)
```
To make use of the Conversation Chain, we need to pass the query:
```python
query = "Describe in few sentences the Parking Spot Policy "
result = conversation_chain.invoke({"question":query})
print(result["answer"])
```

# Now integrating with Gradio GUI

```python
def chat(message,history):
    result = conversation_chain.invoke({"question":message})
    return result["answer"]

# And passing the function chat to Gradio:
view = gr.ChatInterface(chat, type="messages").launch(inbrowser=True)
```
--- 

# How to Troubleshoot RAG with Langchain
![langchain_troubleshoot](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_langchain_troubleshooting.png)


There are different points in the RAG pipeline that can be troubleshooted 
- Queries:
    - 1. Evaluating queries allows you to know what has been pass to the model as prompt, what retrived vector as been found.
         callbacks=[StdOutCallbackHandler()] helps to do this
    
    - 2. Using PromptTemplate: Langchain comes with default prompt. You can override it with your own version:
         combine_docs_chain_kwargs={"document_variable_name": "context"}
    
    - 3. Passing larger number of chunks in the retriver can help (more data can help)
         Consider Using search_kwargs to Optimize Retrieval
           retriever= vectorstore.as_retriever(search_kwargs="k": 5) ->> K nearest neighbour search in vector space
         
    - 4. Experiment with Temperature:
         If accuracy is critical, try lowering it (e.g., temperature=0.2)

### 1. Evaluating queries with callbacks
```python
from langchain_core.callbacks import StdOutCallbackHandler

llm = ChatOpenAI(temperature=0.5,model=Model)
memory= ConversationBufferMemory(memory_key="chat_history",return_messages=True)
retriever= vectorstore.as_retriever()

conversation_chain = ConversationalRetrievalChain.from_llm(
    llm=llm, 
    retriever=retriever, 
    memory=memory, 
    callbacks=[StdOutCallbackHandler()]
)
```
To execute *conversation_chain* use the *.invoke* method and we pass manually a query
```python
query = "how much cost the SHOWER HEAD?"
result = conversation_chain.invoke({"question":query})
answer = result["answer"]
print("\nAnswer:",answer)
```

### 2. Using Prompt Template in Langchain
This ensures that every query uses a consistent structure when interacting with the LLM.

LangChain use its default prompts. This can be optimized further by creating your own prompts and connecting them into the conversation chain.

In your code, you can see two distinct prompts being used:

A prompt for question rephrasing ("Given the following conversation...")
A system prompt for answering questions ("Use the following pieces of context...")

To use your custom prompt template, you need to pass it to the ConversationalRetrievalChain when initializing it.
```python
# Define a default prompt template
from langchain.prompts import PromptTemplate

prompt_template = PromptTemplate(
    input_variables=["context", "chat_history", "question"],
    template=(
        "You are an expert assistant. Use the following retrieved context to answer the user's question."
        " If the answer is not found in the context, say that you don't know.\n\n"
        "Chat History:\n{chat_history}\n\n"
        "Context:\n{context}\n\n"
        "User Question:\n{question}\n\n"
        "Answer:"
    )
)
```
> Note: I found issues with Langchain default prompt. The model changed from [En] to [Sp] without a reason. It might comes from the prompt.   
> [Default]   
> "Prompt after formatting:  
> Given the following conversation and a follow up question, rephrase the follow up question to be a standalone question, in its original language."  


If you  to customize the question rephrasing prompt
```python
condense_prompt_template = PromptTemplate(
    input_variables=["chat_history", "question"],
    template=(
        "Given the following conversation and a follow up question, rephrase the follow up question to be a standalone question.\n"
        "Chat History:\n{chat_history}\n"
        "Follow Up Input: {question}\n"
        "Standalone question:"
    )
)
```

Finally connecting the prompt into the *conversation_chain*
```python
llm = ChatOpenAI(temperature=0.5,model=Model)
memory= ConversationBufferMemory(memory_key="chat_history",return_messages=True)
retriever= vectorstore.as_retriever()

conversation_chain = ConversationalRetrievalChain.from_llm(
    llm=llm, 
    retriever=retriever, 
    memory=memory, 
    callbacks=[StdOutCallbackHandler()],
    combine_docs_chain_kwargs={
        "document_variable_name": "context",
        "prompt":prompt_template # Pass your prompt here
    },
    condense_question_prompt=condense_prompt_template  # Custom question rephrasing prompt
)
```
### 3. Passing larger number of chunks in the retriver
Consider Using search_kwargs to Optimize Retrieval

retriever= vectorstore.as_retriever(search_kwargs="k": 5) ->> K nearest neighbour search in vector space

```python
llm = ChatOpenAI(temperature=0.5,model=Model)
memory= ConversationBufferMemory(memory_key="chat_history",return_messages=True)
retriever= vectorstore.as_retriever(search_kwargs={"k":5}) #K nearest neighbour search in vector space

conversation_chain = ConversationalRetrievalChain.from_llm(
    llm=llm, 
    retriever=retriever, 
    memory=memory, 
    callbacks=[StdOutCallbackHandler()],
    combine_docs_chain_kwargs={
        "document_variable_name": "context",
        "prompt":prompt_template # Pass your prompt here
    },
    condense_question_prompt=condense_prompt_template  # Custom question rephrasing prompt
)
```

### 4. Experiment with Temperature:
If accuracy is critical, try lowering it (e.g., temperature=0.2)
```python
llm = ChatOpenAI(temperature=0.5,model=Model)
memory= ConversationBufferMemory(memory_key="chat_history",return_messages=True)
retriever= vectorstore.as_retriever(search_kwargs={"k":5}) #K nearest neighbour search in vector space

conversation_chain = ConversationalRetrievalChain.from_llm(
    llm=llm, 
    retriever=retriever, 
    memory=memory, 
    callbacks=[StdOutCallbackHandler()],
    combine_docs_chain_kwargs={
        "document_variable_name": "context",
        "prompt":prompt_template # Pass your prompt here
    },
    condense_question_prompt=condense_prompt_template  # Custom question rephrasing prompt
)
```
