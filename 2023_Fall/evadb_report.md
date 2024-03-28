# URL Summization Project

**link to the application on Github** :   https://github.com/Pb314314/EvaDB_project

**Bo Pang:   bpang42@gatech.edu**

**Please read the GitHub report version. There may be some changes.**

## implementation details

In this project, I completed the web summarization task using evadb. Users provide a URL, and the software generates text summarization for that website.

### 1. Allow users to input the URL they want to summarize. Use ReportLab and Beautiful Soup (bs4) to fetch web page text and create a PDF.

![image-20231125234606720](/Users/pb/Documents/My-study-notes/pictures/image-20231125234606720.png)

The `text_list` obtained contains 83 paragraphs. If we use this list to generate a PDF, there will be too many paragraphs. Additionally, each paragraph has too few words, which is not good to summarization. **We need to merge paragraphs**.

### 2. Give user recommended summarization length and use user input length to create paragraphs and create pdf.

![image-20231125235822663](/Users/pb/Documents/My-study-notes/pictures/image-20231125235822663.png)

By merging, ensure that each paragraph has more than threshold number of words. In the end, there are **only 7 paragraphs**.

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20231017233142990.png" alt="image-20231017233142990" style="zoom: 50%;" />

Generate a PDF using the merged list.

### 3. Load pdf to evadb, load model and do the summarization.

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20231126000711820.png" alt="image-20231126000711820" style="zoom:50%;" />

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20231126000729939.png" alt="image-20231126000729939" style="zoom:50%;" />

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20231126000743645.png" alt="image-20231126000743645" style="zoom:50%;" />

### 4. Merge and output the obtained summarization.

Merge the results of summarization into a single string and output its length and content.

![image-20231125235911144](/Users/pb/Documents/My-study-notes/pictures/image-20231125235911144.png)



### 5.Users can choose whether they are children. If they are children, a different model will be used to generate simpler summarization

![image-20231126225319229](/Users/pb/Documents/My-study-notes/pictures/image-20231126225319229.png)

### 6. I have saved the generated summarization as a local PDF, which users can open and read. This helps to avoid the loss of content

![image-20231126231410819](/Users/pb/Documents/My-study-notes/pictures/image-20231126231410819.png)

## sample input

URL = "https://buzzdb-docs.readthedocs.io/part1/lab1.html"

This webpage belongs to CS6422, lab1. It contains a **substantial amount of text**, making it suitable for our testing purposes.

As you can see, this webpage has **a total of 1813 words**. 

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20231017230222222.png" alt="image-20231017230222222" style="zoom: 33%;" />

I recommended a reasonable summary word count to the user. By controlling the summary method through the word count input by the user, the final number of summaries meets the user's requirements.

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20231017230052647.png" alt="image-20231017230052647" style="zoom:33%;" />

## sample output

**Evadb output:**

```text
The goal of this assignment is to help you brush up your C++ programming skills, and exercise yourskills in Data Structure and Algorithm Design. In this assignment, you are to develop a word locatorprogram written in C++. The program will allow a user to check if a specified (re)occurrence of a specified word appears in the input text file. If a bad command is entered,print the precise string ERROR: Invalid command, and go to the next prompt. Examples of badcommands are: "find word 7" and "locate song". Other examples of bad command include the locatecommand having a word that is not legal as per the definition above. For example ra#s and rats! areinvalid word parameters. Find prince 1 No matching entry > new > locate song 1 No matches entry > end. Your main designtask is to pick a tree-based data structure (6422 students do not use a hash-based index structure!.4420 students can.) that allows efficient execution of the locate command. For this assignment, I am not concerned with the efficiency of the load command. You must submit your code (see below) as well as an optional one-page writeup (in REPORT.md) Your program must be written only in C++. Each file should start with a header describing the purpose of the file and should alsocontain your name, GT UserID, and GT email address. Valgrind can be used to detect such memory leaks as well. C++ does not have automatic garbage collection, so each new must ultimately bematched by a corresponding delete. Some popular debuggers for C++ are gdb, lldb, and Visual Studio. You can find more information about valgrind at: http://www.valgrind.org/docs/manual/index.html.
```

Total 271 words.

## Metrics measuring 
### Time: 

For this website: "https://buzzdb-docs.readthedocs.io/part1/lab1.html," it has a total of 1813 words. 

Summarization was performed on all content, and it took a total of 3 minutes in Colab. 

On average, for every 100 words, it took 10 seconds to run.

**10 seconds per 100 words on average(Running in mac m1 Pro CPU on Colab) **

### Summarization behavior:

The origin website has **a total of 1813 words**.

Through summarization, **271 words short passage** was generated. 

**Users can also adjust the ratio by themselves**. Currently, we use 300 words per paragraph, and the summarization result for each paragraph should be less than 142 words. If the user requires a shorter result, they can have each paragraph contain more words.

It is worth noting that the running time is related to the user's input on the upper limit of the word count for summarization.

## lessons learned

1. **Integration of Evadb and AI**: This project provided me with valuable insights into integrating Evadb with artificial intelligence. My proficiency in utilizing Evadb has significantly improved, and I've comprehended the full cycle of data management - from acquisition and storage to processing within Evadb.
2. **Synergy of AI and Databases**: I've come to understand the advantages of melding AI with database technologies. This fusion enhances data processing, making it not only efficient but also remarkably user-friendly, particularly in the context of large-scale data analysis.
3. **Web Content Handling and AI Utilization**: Through this project, I've honed my skills in web content retrieval and converting text information into PDF format. Additionally, I've acquired practical experience in applying AI models for data analysis and summarization, an essential skill set in the current tech landscape.

## challenges faced during implementation

1. Because the `facebook/bart-large-cnn` model for summarization has a maximum limit of 142 words, the initial web page had paragraphs with too few words, causing the summarization to result in more words than the original text. I resolved this issue by merging paragraphs.
2. Because I needed to keep the paragraphs information in the PDF. Initially, all the text was saved together, but by changing my code, I separated the paragraphs.
3. I had no experience in web content retrieval, so obtaining web content was a new learning experience for me.
4. I encountered this error locally, and I couldn't download `pymuppdfs`, so I decided to complete the task on Colab.

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20231017224927679.png" alt="image-20231017224927679" style="zoom:50%;" />

5. Integrating the model. Generating suitable summarizations based on user attributes requires analysis of the users and the appropriate model.

6. In the future, it might be possible to record user information in a database to avoid repeatedly asking for information.

## references

**EvaDB:**https://evadb.readthedocs.io/en/latest/source/usecases/text-summarization.html#ai-query-using-registered-functions

**Hugging Face:**https://huggingface.co/docs/transformers/tasks/summarization

