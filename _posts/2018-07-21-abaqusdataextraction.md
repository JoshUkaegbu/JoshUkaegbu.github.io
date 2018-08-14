---
title: "Abaqus Data Extraction Tool"
date: 2018-07-21
tags: [Data Extraction]
header:
  image: "images/abaqus/abaqus.png"
---

This is a script that I created to extract data from a numerical analysis
engineering software called Abaqus.

Abaqus fully supports writing python scripts for it.

The script extracts data from abaqus, and then writes it into excel for easier
manipulation.

Python code block:
```python
import numpy as np
from xlwt import Workbook

#Asks for inputs to find npz file
job=raw_input('Enter job name (case sensitive):')                  
instance=raw_input('Enter instance name:').upper()
set_name=raw_input('Enter set if required, press enter if not:').upper()
element_type=raw_input('Enter element type:').upper()
invariant=raw_input('Enter invariant required:').upper()

#logic check for set_name input                                               
x=not set_name
if x==True:

 #Creates file name and loads file   
    npz=np.load(job +'_'+ instance +'_'+ element_type +'_'+ invariant +'.npz')              
    npz_list=npz.files
    excelfile=job +'_'+ instance +'_'+ element_type +'_'+ invariant

else:
    npz=np.load(job +'_'+ instance +'_'+ set_name +'_'+ element_type +'_'+ invariant +'.npz')
    npz_list=npz.files
    excelfile=job +'_'+ instance +'_'+ set_name +'_'+ element_type +'_'+ invariant

z=[]

#remove 'QA' from list as it does not contain invariant data
npz_list.remove('QA')

#Appends data from nested list to new variable
for i in range(0,len(npz_list)):
    z.append(npz[npz_list[i]])

#Writes invariant data from z to excel file with headings
wb=Workbook()
m=0
sheet1=wb.add_sheet('sheet1')
while m < len(npz_list):

    for i in range(0,len(z[1])):
        sheet1.write(i+1,m,float(z[m][i]))

    sheet1.write(0,m,npz_list[m])
    m=m+1


wb.save(excelfile+'.xls')
```
