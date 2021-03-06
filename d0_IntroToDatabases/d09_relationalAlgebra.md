# Relational Algebra
- example: simple college admission db (italicized are unique ids/keys)
    - college(*cName*, state, enrollment)
    - Student(*sID*, sName, GPA, sizeHS)
    - Apply(*sID, cName, major*, decision)
- simplest query: relation name
- use operators to filter, slice, combine
- operators so far:
    - select, project, cross-product, natural join, theta join
- **NOTE**: And (wedge or ∧) and Or (vee or ∨)

## Relations (σ or sigma)
- simplest query: **relation** name (σ <sub>condition</sub> Relation)
    - use operators to filter, slice, combine 
    1. Students with GPA>3.7
        - σ<sub>GPA</sub> > 3.7 student
    2. Students with GPA>3.7 and HS < 1000
        - σ<sub>GPA</sub> >3.7 ^ HS < 1000 Student
    3. Applications to Stanford CS major
        - σ <sub>cName</sub> = 'Stanford' ^ major = 'cs' Apply

## Project (π or pi)
- **Project** Operator: picks certain columns (denoted with pi π) (pi <sub>A1,..An</sub> Rel)
    - ID and decision of all applications
    - π <sub>sID, decision</sub> Apply: this will make relation with 2 rows
        - have all tuples of applybut only the sID and decision columns
    - ID and name of students with GPA>3.7 
        - π <sub>sID,sName</sub>(σ <sub>GPA</sub> > 3.7 Student)

### Video Quiz
- Q1: State which statements are true or false
    - Statement A: It can be useful to compose two selection operators.
        - For example: σ<sub>state</sub>=‘CA′(σ<sub>enr>8000</sub>College)
    - Statement B: It can be useful to compose two projection operators.
        - For example: π<sub>GPA</sub>(π<sub>sID,GPA,HS</sub>Student)
- A: Both statements are false
    - Two selection operators in a row can always be replaced by a single selection operator whose condition is the "and" of the two selection conditions. If there are two projection operators in a row, the attribute list of the second (outer) projection must be a subset of the attribute list of the first (inner) projection. Thus, the first projection can be removed without changing the result of the expression.

## Duplicates ( X )
- List of application majors and decision
    - π<sub>major,dec</sub>Apply
- SQL operates on Multisets, bag (doesn't eliminate duplicates)
- relation alg. based on sets so it does eliminate dupes
- **cross-product:** combine two relations (aka Catesian product)
    - Student x Apply 
        - we'll have S tuples x A tuples = one tuple for ever combination of tuples from student apply relation
    - Names and GPA of students with HS>1000 who applied to CS and were rejected
        - π<sub>cName,GPA</sub>(σ<sub>student.sID=Apply.sID ^ HS >1000 ^ major='cs' ^ dec="R"</sub>(Student x Apply))

### Video Quiz
- Q2: Which of the following expressons does NOT return the names and GPAs of students with HS>1000 who applied to CS and were rejected?
    - σ<sub>Student.sID=Apply.sID</sub>(π<sub>sName,GPA</sub>(σ<sub>HS>1000</sub>Student x σ<sub>major='CS'^dec='R'</sub>Apply))
- A: expression is invalid because the sID attributes are not included in the result of the expression to which condition Student.sID=Apply.sID is applied.

## Natural Join (uses bowtie ⋈, but I'll use infinity symbol ∞)
- enforce equality on all attributes with same name 
- Eliminate one copy of duplicate attributes
- Names and GPA of students with HS>1000 who applied to CS and were rejected
    - π<sub>sName,GPA</sub>(σ<sub>HS>1000 ^ major='cs' ^ dec='R'</sub> (Student ∞ Apply))
- Names and GPA of students with HS>1000 who applied to CS at college with enr>20,000 and were rejected
    - π<sub>sName,GPA</sub>(σ<sub>HS>1000 ^ major='cs' ^ dec='R ^ enr>20000'</sub> (Student ∞ (Apply ∞ College)))
- Exp1 ∞ Exp2 == π<sub>schema(E1) U Schema(E2)(</sub>(σ<sub>E1A1 = E2A1 ^ E1A2=E2A2 ^ ...</sub>)(Exp1 x Exp2))

### Quiz
- Q3: Which of the following English sentences describes the result of this expression:
π<sub>sName,cName</sub>(σ<sub>HS>enr</sub>(σ<sub>state=‘CA‘</sub>College⋈Student⋈σ<sub>major=‘CS‘</sub>Apply))
    - A: Students paired with all California colleges smaller than the student's high school to which the student applied to major in CS 
        - The inner natural join connects students with the colleges to which they've applied, allowing only California colleges and CS-major applicaitons. The outer selection condition filters out all applications except those where the high school is bigger than the college, and the final projection keeps the student and college names.
- Q4: Which of the following expressions finds the IDs of all students such that some college bears the student's name?
    - πsID(College⋈Student)
    - πsID(σcName=sName(College×Student))
    - πsID(πcNameCollege⋈πcName(σsName=cNameStudent))
    - πsID(σcName=sName(πsIDStudent×College×Student)
    - A: π<sub>sID</sub>(σ<sub>cName=sName</sub>(College×Student))
        - The first choice returns the IDs of all students in the database. The third choice is invalid because cName is not an attribute of Student. The fourth choice yields all sIDs in Student.

## Theta Join (θ)
- Exp1 ∞<sub>Θ</sub> Exp2 ≡ σ<sub>Θ</sub> (Exp1 x Exp2) 
- Basic operation implemented in DBMS
- term "join" often means theta join
    - take two relation, combine all tuples, but only keep combo that pass θ condition

