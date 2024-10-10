# IRAG - Short Document with IMPROVE RAG

## Abstract
Project นี้เริ่มต้นจากความพยายามของเราที่จะนำการสร้างข้อความด้วยการดึงข้อมูล (Retrieval-Augmented Generation หรือ RAG) มาใช้สำหรับระบบถามตอบทั่วไปที่เกี่ยวข้องกับเอกสาร 
อย่างไรก็ตาม เราไม่สามารถทำได้อย่างมีประสิทธิภาพเนื่องจากปริมาณข้อมูลที่มากและซับซ้อน ทำให้วิธีการ RAG แบบปกติไม่เพียงพอ Project นี้ได้รับการออกแบบมาเพื่อให้ทำงานได้ดีโดยเฉพาะกับเอกสารที่มีขนาดสั้น
เนื่องจากงานนี้เกี่ยวข้องกับเอกสารที่สั้นกว่า ทำให้วิธีการนี้มีประสิทธิภาพมากขึ้น นี่คือเหตุผลที่พัฒนาโครงการนี้ขึ้นมา <br>

ซึ่งจะเน้นไปที่การสร้าง Vector Store โดยสร้างเอง ซึ่งจะเก็บในรูปแบบ CSV ซึ่งจะเก็บข้อมูลต่างๆผ่าน CSV โดยจะแยก Columns ว่าเป็น Content หรือ Embedding เพื่อให้สามารถ Search ได้
ซึ่งที่ผ่านมายังไม่พบข้อเสีย แต่ข้อดีหลักๆคือ <br>
1. เก็บผ่าน Local ได้
2. ไม่ต้องหา Lib ที่เหมาะกับงาน
3. ปรับแต่งได้ทุกจุด
4. สามารถ Merge ระหว่าง PDF และ CSV และ Search ได้อย่างมีประสิทธิภาพมากๆ

## Find Problem
1. PDF document <br>
  1.1 Extract text from PDF ค่อนข้างมีปัญหา <br>
      &nbsp;&nbsp;&nbsp;&nbsp;Text มีอาการขาดหาย ตัวสระมีเว้นช่องว่าง <br>
     ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/R2.png) <br>
  1.2 พบปัญหาและประสิทธิภาพของ Chunk size <br>
      &nbsp;&nbsp;&nbsp;&nbsp;เนื่องจากแต่ละ Docs ความยาวประโยคไม่เหมือนกัน ทำให้มีปัญหาในการเลือก Chunk size และถึง Chunk size จะเท่ากันก็ตาม แต่ประสิทธิภาพในการค้นหาก็ไม่เท่ากัน จะเห็นได้ว่า ถ้า chunk size น้อย อาจจะไม่ได้อ่าน Docs ที่มีคุณภาพมากนัก อีกทั้งต้องมาหาว่าต้องใช้ chunk เท่าไหร่ดี แต่ว่าก็ค่อนข้างมีปัญหา<br>
     ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/R1.png) <br>
   
2. CSV document <br>
   2.1 Loss Columns of CSV Data <br>
     &nbsp;&nbsp;&nbsp;&nbsp;คือการที่บาง Vector Store จัดเก็บแค่ Text ของ Rows ไม่ได้มี Attribute ที่เป็นส่วนคัญมากๆ ของ Structure Data ก็คือ ใน 1 Chunk อาจจะเป็น 3 row ครึ่ง หรือ 2.75 row ย้ำว่าใน 1 Chunk ซึ่ง LLM ก็จะได้อ่าน Garbage 
     ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/losscolumn.png) <br>
   2.2 Misunderstand <br>
     &nbsp;&nbsp;&nbsp;&nbsp;เนื่องจากมีการศูนย์เสีย Attribute ที่เป็นส่วนคัญมากๆไป ทำให้มีแต่เนื้อหา อย่างเช่นตัวอย่างนี้ LLM อาจจะเข้าใจว่ามันคือ model ตระกูล Transformer ที่ชื่อ Wachawich แต่จริงๆแล้วมันคือชื่อจริง ชื่อเล่น และหนังที่ชอบ!!!
     ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/misunderstand.png) <br>

## Pipeline
1. PDF <br>
  ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/PPDF.png)
2. CSV <br>
 ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/CSVV.png)

## Tech
### Model
พอดีว่า Project นี้ใช้ GCP ทำให้มี Claude ให้ใช้และมีใน Langchain พอดี เลยเลือกใช้ตัวนี้ <br>

```python
project = "<project>"
location = "<location>"

llm = ChatAnthropicVertex(
    model_name="claude-3-5-sonnet@20240620",
    project=project,
    location=location,
    temperature=0.1,
    max_tokens=6046,
    timeout=None,
) 
```
<br>
หรือว่าจะใช้ตัว GPT ก็ได้

```python
import getpass
import os
from langchain_openai import ChatOpenAI

if not os.environ.get("OPENAI_API_KEY"):
    os.environ["OPENAI_API_KEY"] = getpass.getpass("Enter your OpenAI API key: ")

llm = ChatOpenAI(
    model="gpt-4o",
    temperature=0.1,
    max_tokens=6046,
    timeout=None,
)
```
### Embedding Model
```python
import key_param as key_param
import os
from openai import OpenAI

os.environ["OPENAI_API_KEY"] = key_param.OPENAI_API_KEY
client = OpenAI()

def get_embedding(text, model="text-embedding-3-small"):
   text = str(text)
   text = text.replace("\n", " ")
   
   return client.embeddings.create(input = [text], model=model).data[0].embedding
```

หรือว่าจะใช้ตัวอื่นก็ได้ เช่น
<br>

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')
doc_embeddings = model.encode(documents)
```
<br>
<br>
## Step
1. Create Vector Store ตาม code CreateVectorStore/... <br>
2. นำทั้ง PDF และ CSV มารวมกันที่ MergeVectorStore
3. อ่าน MergeVectorStore.md
