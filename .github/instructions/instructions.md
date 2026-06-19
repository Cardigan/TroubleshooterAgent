# Troubleshooting `.ai` folder conventions

> These conventions are referenced by `agents/troubleshooter.md` and the
> `skills/troubleshoot-*` skills as the source of truth for how the `.ai`
> investigation folder is structured and maintained. They are copied verbatim
> from the author's global Copilot instructions.

## Notes and the .ai folder for troubleshooting Projects 
- Each project may have a `.ai` folder this is a place for the ai to write notes, paths forward, and other documentation that the user can refer to later. The user may ask the ai to write notes in this folder as they investigate and troubleshoot issues. The ai should write in a clear and concise manner, using markdown formatting for readability. The ai should also include any relevant links to documentation or resources that may be helpful for the user.
- poc means possible cause.
- Each new entry should start with a number and a title, "02-hypotheses.md" 
- the number should count up with each new entry.
- The title should be a brief description of the content of the note, such as "Hypotheses for Issue X" or "Next Steps for Investigation Y".
- Keep notes on topic, create new notes as needed. 
- within each note, use headdings, and bullet points to organize information and make it easy to read.
- there should be a tracking notes file, "00-tracking.md" that has a table of contents with links to each note, and a brief description of each note. This will help the user keep track of their investigation and easily find relevant information as they continue to troubleshoot.
- 00-tracking.md should be updated with each new note, and it maintains a breif description of open loops, undone tasks, open quesions, and links to relevant notes. The tracking file itself should not contain detailed information, but rather serve as a roadmap for the investigation, with links to the more detailed notes in the .ai folder.
- any new section that is added should come with a date, and name of the model that generated it
- you may edit previous entrires, but this should be avoided. If a section is outdated, make a note in the begining of the section and link to the new section. Be sure to date and sign any edits you make to previous entries. 
    - use this format for edits: 
    ```
    [Date] - [Model Name] - [Description of Edit]
    ```
- At the end of each session review the notes in the `.ai` folder update the tracking file "00-tracking.md" with any new notes or changes to existing notes.
- When doing a review of a project, review it as 3 agents, each with a different perspective:
  1. **Skeptical Agent**: Reviews the pocs with a critical eye, questioning assumptions, looking for potential flaws, and ensuring that conclusions are well-supported by evidence.
  2. **Optimistic Agent**: Reviews the pocs with a positive perspective, identifying potential reasons why the pocs might be valid conclusions.
  3. **Judge Agent**: Provides an unbiased perspective, balancing the views of the Skeptical and Optimistic agents, and ensuring that the review is fair and comprehensive. This is the agent that makes the final assessment of the pocs based on the input from the other two agents. This is the agent that updates the .ai folder after a review. 


  
 ## "Make a note" convention
 - **Default action:** When the user says **"make a note"** and pastes content, save the **full pasted content verbatim** into a **new numbered note** in the `.ai` folder (e.g., `07-new-evidence.md`), with a header containing the **date** and the **model name** (e.g., `**Date:** YYYY-MM-DD` / `**Model:** <model>`). Do not summarize, truncate, or paraphrase the pasted content.
 - Increment the note number per the standard `.ai` numbering. Give the file a brief descriptive kebab-case title.
 - Update `00-tracking.md` (TOC + changelog) to reference the new note.
 - You may add a brief summary header and a short "why this is relevant" line above the verbatim content, but the original text must remain intact. Mark any speculation clearly as such.
 - Before creating a new note, check whether the same content was just saved (avoid duplicates); if it already exists, say so instead of duplicating.
 - Exceptions (only if the user explicitly asks): very short content may go inline in the relevant note/tracking file; very long raw dumps may go in `.ai/attachments/` with a descriptive kebab-case filename and a link from the relevant note.
