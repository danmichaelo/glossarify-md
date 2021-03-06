# [Substring Test Document](#substring-test-document)

## [A](#a)

GIVEN a term '[A][1]' WITH '[A][1]' being atomic
 AND itself being substring of terms '[AB][2]' AND '[ABC][3]',
THEN '[A][1]' MUST be correctly linked to its definition of '[A][1]'
 AND other terms MUST NOT be affected by the definition of '[A][1]'.

## [AB](#ab)

GIVEN a term '[AB][2]' WITH another term '[A][1]' being substring of it,
 AND itself being substring of '[ABC][3]'
THEN '[AB][2]' MUST be linked as a single term to its definition of '[AB][2]'
 AND '[AB][2]' MUST NOT be split by a link to the definition of '[A][1]'.

## [ABC](#abc)

GIVEN a term '[ABC][3]' WITH other terms '[AB][2]' AND '[A][1]' being substrings of it,
THEN '[ABC][3]' MUST be linked as a single term to its definition of '[ABC][3]'
 AND '[ABC][3]' MUST NOT be split by a link to the definition of '[AB][2]'
 AND '[ABC][3]' MUST NOT be split by a link to the definition of '[A][1]'

## [Ä](#ä)

GIVEN a non-ASCII term '[Ä][4]' WITH '[Ä][4]' being atomic
 AND itself being substring of terms ['ÄÖ'][5] AND ['ÄÖÜ'][6],
THEN '[Ä][4]' MUST be correctly linked to its definition of '[Ä][4]'
 AND other terms MUST NOT be affected by the definition of '[Ä][4]'.

## [ÄÖ](#äö)

GIVEN a non-ASCII term ['ÄÖ'][5] WITH another term '[Ä][4]' being substring of it,
 AND itself being substring of ['ÄÖÜ'][6]
THEN ['ÄÖ'][5] MUST be linked as a single term to its definition of ['ÄÖ'][5]
 AND ['ÄÖ'][5] MUST NOT be split by a link to the definition of '[Ä][4]'.

## [ÄÖÜ](#äöü)

GIVEN a non-ASCII term ['ÄÖÜ'][6] WITH other terms ['ÄÖ'][5] AND '[Ä][4]' being substrings of it,
THEN ['ÄÖÜ'][6] MUST be linked as a single term to its definition of ['ÄÖÜ'][6]
 AND ['ÄÖÜ'][6] MUST NOT be split by a link to the definition of ['ÄÖ'][5]
 AND ['ÄÖÜ'][6] MUST NOT be split by a link to the definition of '[Ä][4]'

[1]: ./glossary.md#a "GIVEN an atomic term 'A' WITH term 'A' being a substring of 'AB' and 'ABC'"

[2]: ./glossary.md#ab "GIVEN a term 'AB' WITH term A being a substring of it and itself being substring of 'ABC'"

[3]: ./glossary.md#abc "GIVEN a term 'ABC' WITH terms 'AB' and A being substrings of it"

[4]: ./glossary.md#ä "GIVEN an atomic non-ASCII term 'Ä' WITH term 'Ä' being a substring of 'ÄÖ' and 'ÄÖÜ'"

[5]: ./glossary.md#äö "GIVEN a term 'ÄÖ' WITH term 'Ä' being a substring of it and itself being substring of 'ÄÖÜ'"

[6]: ./glossary.md#äöü "GIVEN a term 'ÄÖÜ' WITH terms 'ÄÖ' and 'Ä' being substrings of it"
