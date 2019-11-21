# mesh-stripping
This python file strips the Medical Education Search Heading (MeSH) XML file, 
to create a csv which can be bulk uploaded into an empty SQL table.  
MeSH terms are tagged to all articles submitted to pubmed and are useful
controlled vocabularly for biology and science in general.  There is often
a need to use the terms in a relational database, hence this script. 

The script can be 
RUN BY TYPING BELOW IN A TERMINAL WITH PYTHON SET UP (takes ~20sec to run)
   2 WILL BE CREATED = "output.csv" & "output.txt"
> python3 meshFromDescXML.py

In the python file, modify the filename 'desc2020.xml' to be whatever desc file 
you are using.

MODIFY TO WHATEVER MESH XML FILE YOU ARE PARSING BELOW, HAVE IN SAME DIRECTORY
tree = ET.parse('desc2020.xml')
root = tree.getroot()
itemArrayS = []
# THE BELOW LINE ADD ONE HEADER ROW TO FILE, BUT UNDESIRED IF IMPORTING INTO ALREADY CREATED EMPTY SQL TABLE
#itemArrayS.extend([['meshTerm', 'synonym']])

for QR in root.findall('.//DescriptorRecord'):
   conceptNameString = QR.find('ConceptList/Concept/ConceptName/String').text
   for element in QR.findall('ConceptList/Concept/TermList'):
      itemArray = []
      if element.find('Term') is not None:
         terms = [terms.text for terms in element.findall('Term/String')]
      i=0
      #itemArray=[]
      while i < len(terms):
         #itemArray.append(conceptNameString,terms[i])
         itemArray.extend([[conceptNameString, terms[i]]])
         i += 1
      itemArrayS.extend(itemArray)

   # DEBUGGING LINES WHICH WILL PRINT OUT IN TERMINAL IF UNCOMMENTED
   #print(conceptNameString , " , terms = " , terms , " , len(terms) = " , len(terms))
   #print("itemArray = " , itemArray)
   #print("itemArrayS = " , itemArrayS)

# PRINT TO TERMINAL HOW MANY ROWS ARE BEING WRITTEN
print("itemArrayS len = " , len(itemArrayS))

# WRITE ARRAY OF MESH TERMS AS TALL TABLE WITH 2 COLUMNS: conceptNameString, term[i]
#  THESE CAN BE BULK INSERTED INTO AN EMPTY SQL TABLE.
#   I SET MINE UP WITH COLUMN NAMES = meshTerm , synonym
#   THE OFFICIAL MESH TERM HAS A ROW WITH ITSELF AS SYNONYM SO THE SYNONYM COLUMN
#   CAN BE SEARCHED TO FIND ALL TERMS.
print("itemArrayS = " , itemArrayS , file=open("output.txt", "a"))

with open("output.csv", "w", newline="") as f:
   writer = csv.writer(f)
   writer.writerows(itemArrayS)




