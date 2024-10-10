# IRAG - Short Document with IMPROVE RAG

## Abstract
Project นี้เริ่มต้นจากความพยายามของเราที่จะนำการสร้างข้อความด้วยการดึงข้อมูล (Retrieval-Augmented Generation หรือ RAG) มาใช้สำหรับระบบถามตอบทั่วไปที่เกี่ยวข้องกับเอกสาร <br> 
อย่างไรก็ตาม เราไม่สามารถทำได้อย่างมีประสิทธิภาพเนื่องจากปริมาณข้อมูลที่มากและซับซ้อน ทำให้วิธีการ RAG แบบปกติไม่เพียงพอ Project นี้ได้รับการออกแบบมาเพื่อให้ทำงานได้ดีโดยเฉพาะกับเอกสารที่มีขนาดสั้น <br>
เนื่องจากงานนี้เกี่ยวข้องกับเอกสารที่สั้นกว่า ทำให้วิธีการนี้มีประสิทธิภาพมากขึ้น นี่คือเหตุผลที่พัฒนาโครงการนี้ขึ้นมา

## Find Problem
  1. Extract text from PDF ค่อนข้างมีปัญหา
      Text มีอาการขาดหาย ตัวสระมีเว้นช่องว่าง
     ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/R2.png)
  2. พบปัญหาและประสิทธิภาพของ Chunk size
      เนื่องจากแต่ละ Docs ความยาวประโยคไม่เหมือนกัน ทำให้มีปัญหาในการเลือก Chunk size และถึง Chunk size จะเท่ากันก็ตาม แต่ประสิทธิภาพในการค้นหาก็ไม่เท่ากัน
     ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/R1.png)

## Solution
1. PDF
  ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/PPDF.png)
2. CSV
 ![Exercise](https://github.com/wachawich/IRAG-Short-Docs-with-RAG/blob/main/Image/CSVV.png)
