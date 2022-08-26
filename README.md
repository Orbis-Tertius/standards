# Orbis coding standards

All production-track code for Orbis should adhere to these standards. Where code
does not adhere to these standards, we can consider it as technical debt.

These coding standards do not cover issues of verification or correctness
or performance. These coding standards apply to achieving code quality, as distinct
from correctness. Code quality is related to the ease of reading and reasoning
about the code. Code quality includes not just the quality of pieces of code in
isolation, but the quality of the code base overall, which includes consistency within and between projects at Orbis Labs.

## Definitions

"Must" indicates a hard requirement.

"Should" indicates a general guideline which may have valid exceptions in some cases.

"Prefer" indicates a desirable outcome which must be weighed against other considerations
in pro/con analysis.

## General guidelines

You should make code self-documenting. Where practical, when code is commented, put
the information in the comment into the code, and delete the code comment.

You should not commit commented out code.

You should note changes that need to be made to code with a code comment containing
the string "TODO." This applies to circumstances where you need to indicate a place
where code is unfinished, and the issue is too small for it to make sense to make
a Github issue about it. We should be able to grep for TODO to find any todos noted
in the code.

Make top-level names self-documenting. A name should contain all the words needed
to describe unambiguously what it names. If the name is long, then the name is long.

Avoid mutable storage locations. When you can create new data instead of modifying
existing data, prefer to create new data.

Prefer data types which cannot represent values that do not have a valid meaning.
For example, if a value cannot validly be negative, then prefer a numeric type which
does not have negative values.

In top-level declarations, types should have names that have a domain-specific
meaning: e.g., "car serial number" instead of "string," or "proposal is approved"
instead of "bool."

Arithmetic must be exact. There is no floating point arithmetic allowed.

## Haskell specific guidelines

Each module must have an export list. Imports should be explicit or qualified.

The following GHC flags must be enabled in CI pipelines for all code:

```
-Werror
-Weverything
-Wno-all-missed-specialisations
-Wno-implicit-prelude
-Wno-missing-safe-haskell-mode
-Wno-prepositive-qualified-module
-Wno-safe
-Wno-unsafe
-threaded
-rtsopts
-with-rtsopts=-N
```

Use Ormolu. CI pipelines must fail if Ormolu makes changes to the source. Ormolu
may be selectively disabled on specific code sections if it butchers them.

Use hlint with the [Orbis standard hlint configuration](https://github.com/Orbis-Tertius/standards/blob/master/hlint.yaml).
CI pipelines must fail if there
are any hlint hints. Hlint hints may be disabled selectively on code sections and
files where they are obnoxious.

Use [vinyl extensible records](https://hackage.haskell.org/package/vinyl)
and composite extensible records instead of native record types.

Use [really-safe-money](https://github.com/NorfairKing/really-safe-money) for
computing all cryptocurrency amounts.

Prefer newtypes over type synonyms.

Prefer monad transformers (MaybeT and ExceptT) over
nested case statements dealing with Maybe or Either values.

Pure code must not throw exceptions, with the following
exception. `die` can be invoked in cases which are logically
impossible. In such cases it is not clear how to proceed,
because the engineer's reasoning about the code is wrong.
In such cases, terminating the program may be the prudent
course of action. For this purpose, `error` and `undefined`
must not be used. `undefined` provides an unhelpful error
message. `error` only terminates the current thread, which
may have unexpected consequences, so it is better to terminate
the whole program, which is what `die` does.
