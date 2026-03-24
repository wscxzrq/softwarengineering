---
name: software-engineering-exam-curation
description: Curates subject exam study notes and question summaries from a user-provided file list. Use when整理或审查题目汇总文件、从文件名包含“真题”的文件提取考点、把题挂到对应知识点后、统一来源标注、去重、或处理学科名文件/其他资料文件。
---

# Software Engineering Exam Curation

## Purpose
Use this skill when maintaining or reviewing a subject question-summary file such as `软件工程题目知识点汇总.md`.

This skill enforces the user's fixed rules for:
- selecting knowledge points
- selecting questions under each knowledge point
- assigning source labels
- deduplicating repeated questions
- preserving the established markdown style

## Core Rule

### 1. Knowledge point source rule
Only derive **knowledge points** from files whose **filename contains `真题`**.

Do **not** use non-`真题` files to create new knowledge point modules.

They may only be used to supplement questions under an already accepted knowledge point.

### 2. Question source rule
Questions under each accepted knowledge point may come from:
- exam files whose filename contains `真题`
- files whose filename is a subject name, such as `软件工程.md`
- all other files in the user-provided file list

But the source labeling rules below must be followed strictly.

## Source Label Rules

### 1. Files with `真题` in the filename
Treat these as true-exam sources.

Label questions using `年份后两位 + 月份两位`, for example:
- `2025年4月考期真题汇总.md` -> `2504`
- `202510真题.md` -> `2510`
- `2024年10月真题.md` -> `2410`

If the month is a single digit in the filename, normalize it to two digits in the label.

Preferred extraction order for the label:
1. Parse year and month from the filename
2. If ambiguous, inspect the file heading for an explicit period marker
3. If still ambiguous, ask the user before assigning a label

### 2. Files whose filename is a subject name
Treat these as `课后习题`.

Examples:
- `软件工程.md`
- `数据结构.md`
- `操作系统.md`

Heuristic:
- if the filename is just the subject/course name with no extra qualifier like `真题` / `简答题` / `汇总` / `押题` / `笔记`, treat it as a subject-name source
- label imported questions from such files as `课后习题`

### 3. All other files
Treat questions from all other non-exam, non-subject-name files as `笔果来源`.

Examples:
- `简答题.md`
- `AI押题.md`
- `重点资料整理.md`

### Hard restriction for non-exam sources
When reading a subject-name file or any other supplemental file:
- do **not** import any question that is already marked as `2504` / `2510` / `202504` / `202510`
- more generally, do **not** import any question explicitly marked with a year-month exam marker like `202404`, `202410`, `202504`, `202510`, or labels like `2404`, `2410`, `2504`, `2510`
- if a question in those files is explicitly a true-exam question, do not relabel it as `笔果来源` or `课后习题`
- skip it instead, unless the same question is already being imported from a real exam file

## Deduplication Rules

### General deduplication
Always compare candidate questions against the current contents of `软件工程题目知识点汇总.md`.

If the same question already exists, do not add it again.

### Treat as duplicates when
Treat two entries as duplicates if any of these are true:
- the stem is identical or nearly identical
- the tested knowledge point is clearly the same and the answer is effectively the same
- one version is only a formatting variant of another
- one version comes from a supplemental source but the same question already exists from `2504` or `2510`

### Preferred retention order
When duplicates exist, keep the strongest source in this priority order:
1. true-exam label such as `2504`, `2510`, `2410`
2. `笔果来源`
3. `课后习题`

If the same question appears in both exam source files, merge into one item and mark both sources, e.g. `【2504 / 2510】`.

If the same question appears in an exam source and a supplemental source, keep the exam-labeled version only.

## Non-deletion Rule
If the user says not to delete, do not remove existing sections or modules casually.

In that case:
- keep the existing structure
- patch mistakes in place
- add missing exam-derived knowledge points
- add missing non-duplicate questions under existing knowledge points
- fix incorrect labels without broad cleanup

## Style Rules

### Knowledge point blocks
When the user wants the knowledge point text to match a review outline, use the corresponding wording from the outline file for the **core knowledge point description**.

But still enforce the separate rule that **knowledge point inclusion** itself must come from files with `真题` in the filename.

### Question formatting
Preserve the existing markdown style in `软件工程题目知识点汇总.md`.

#### Multiple choice
Format as:

```markdown
**1. 题目内容【2504】**

- A. 选项1
- B. 正确选项 ✅
- C. 选项3
- D. 选项4
```

Or with numeric options if the original question uses `1-4`:

```markdown
**1. 题目内容【课后习题】**

- 1. 选项1
- 2. 正确选项 ✅
- 3. 选项3
- 4. 选项4
```

Do not switch back to plain `1. 2. 3. 4.` without list markers for choice questions.

#### Fill-in
Format as:

```markdown
**1. 题目内容【2510】**

**答案**：xxx
```

#### Short answer / definition
Format as:

```markdown
**1. 题目内容【笔果来源】**

答案内容……
```

#### Diagram / image questions
If the source file already provides an image, reuse that image directly.

Do not replace an existing source image with a text placeholder like:
- “自行绘制”
- “按原题作图”
- “题目为作图题”

Preferred templates:

```markdown
**题目图片**：

![image-name](./image-name.png)

**答案**：直接采用题库提供的对应图示。
```

or

```markdown
**题目描述**：……

**题目图片**：

<img src="./image-name.png" alt="..." style="zoom:80%;" />

**问题**：

1. …
2. …

**参考答案**：

1. …
2. …
```

## Review Checklist
Before finalizing any change to `软件工程题目知识点汇总.md`, verify:

- [ ] Every new knowledge point can be traced to a file whose filename contains `真题`
- [ ] No new knowledge point was created only from a subject-name file or other supplemental file
- [ ] Every imported question has the correct source label
- [ ] No year-month-marked question from a non-exam file was mislabeled as `笔果来源` or `课后习题`
- [ ] Repeated questions were not added again
- [ ] If duplicates were found, the higher-priority source label was kept
- [ ] Choice-question formatting matches the established style
- [ ] Existing source images are preserved for diagram/image questions

## Recommended Workflow

1. Read the current target summary file
2. Read the user-provided file list
3. Split the files into 3 buckets:
   - exam files: filename contains `真题`
   - subject-name files: filename is just the subject name
   - other supplemental files
4. From exam files only, identify the actual knowledge points
5. Check which exam-derived knowledge points are missing or incomplete in the current summary
6. For each accepted knowledge point, gather candidate questions from all allowed files
7. Filter non-exam files:
   - exclude any question explicitly marked with year-month exam labels
8. Compare candidates against the current summary and remove duplicates
9. Add only the non-duplicate items with correct labels
10. Re-check formatting, labels, image usage, and label consistency

## Examples

Additional detailed Chinese examples are available in [examples.md](examples.md).

### Example 1: Allowed
- Knowledge point appears in a file named `202510真题.md`
- Matching extra short-answer explanation is found in `简答题.md`
- That question has no year-month exam marker
- Add it under the existing knowledge point and label it `笔果来源`

### Example 2: Not allowed
- A useful concept appears only in `软件工程.md`
- It never appears in any file whose filename contains `真题`
- Do not create a new knowledge point module for it

### Example 3: Deduplication
- A question already exists as `【2504】`
- The same question is found in `软件工程.md`
- Do not add the `课后习题` version

### Example 4: Label correction
- A question currently labeled `【课后习题】`
- Source review shows the exact same question is a `202504` true exam question
- Correct the label to `【2504】`

### Example 5: Subject-name file handling
- File list includes `软件工程.md`
- The filename is the subject name itself
- Questions imported from it are labeled `课后习题`
- But any entry inside it marked `202504` / `202510` must be skipped as a supplemental source

### Example 6: Filename-driven exam label
- File list includes `2024年10月真题整理.md`
- Since the filename contains `真题`, it is an exam source
- All imported questions from that file should use the label `2410`
