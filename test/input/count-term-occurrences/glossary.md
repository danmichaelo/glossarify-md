# Glossary

## Zero

GIVEN a term "Zero"
AND the term "Zero" is mentioned twice in its own definition
AND nowhere else
THEN this term's occurrence count MUST be 0.

## One

GIVEN a term "One"
AND the term is mentioned only once in a subsequent term definition
AND nowhere else
THEN this term's occurrence count MUST be 1.

#### mention-one-once

GIVEN this definition
AND it mentions another term "One" once
AND itself is being mentioned nowhere else
THEN the other term's occurrence count MUST be 1
AND this term's occurrence count MUST be 0.

## Two

GIVEN a term "Two"
AND the term is mentioned term two times in a subsequent term definition
AND nowhere else
THEN this term's occurrence count MUST be 2.

#### mention-two-twice

GIVEN this definition
AND it mentions another term "Two" Two times
AND itself is being mentioned nowhere else
THEN the other term's occurrence count MUST be 2
AND this term's occurrence count MUST be 0.

## Mentioned-in-document-once

GIVEN a term
AND it is mentioned once in a document
AND not in the glossary
THEN this term's occurrence count MUST be 1.

## Mentioned-in-document-twice

GIVEN a term
AND it is mentioned twice in a document
AND not in the glossary
THEN this term's occurrence count MUST be 2.
