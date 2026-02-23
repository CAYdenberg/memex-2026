The working name is **DataKasten.** I think we should change this.

## Purpose

The purpose of DataKasten is to provide a rich-text editor for the sematics of elements found in scholarly journal articles. It differs from other commercial text editors in three important ways:
1. It focuses on content, while leaving presentation aside for the publisher/typesetter
2. It manages citations and figures (external images referred to from the text)
3. Manuscripts are edited as CRDTs, allowing authors to work independently until they are ready to merge their changes. Given the deep nature of scientific writing, this is superior to real time collaboration.

When the manuscript is finished, it can be compiled to PDF, HTML or JATS-XML for submission to a journal. The journal or citation style is used by the compiler to produce citations in the correct format.

## User Classes: Who is this for?

**User:** Usually, a manuscript author. Authors are associated with Projects, projects may have many authors and authors may have many projects. Author metadata can be added for authors who are not actually users.

**Anonymous user:** A user who works on a manuscript without authentication. There are no collaborative features available in this mode.
 
**Invitee:** A person invited to a project by an existing Project user. If the Invitee is already a User they will be automatically added to the project. If not they will be emailed a link to join the project (after signing up).

## Architecture

### Python/Django/Postgres

Foundation for user and project records. Project data (drafts) are synced via Automerge docs stored in Postgres JSONB fields.

### React/Slate

Bulma for styling. React is used for the menus and components surrounding the editor, as well as leaf nodes within the editor.

A lurking issue is that Slate operations will have to be inserted into automerge with high fidelity. The proof-positive is that collecting the operations in automerge and then converting them to Slate state will produce the same state as the operations applied directly to Slate.

### Manubot(web)

[Manubot](https://github.com/manubot/manubot) is a Python tool that operates over Markdown and figures (images) to compile a manuscript for submission. It formats citations according to varying styles that are provided by the user. We will need to modify this tool to act as a web service and read figures from the database where necessary.

### Automerge/IndexedDB

This is the biggest unknown of the project and extra time will be needed to fully explore and understand it.

**Automerge** is a CRDT for JavaScript with rich-text capabilities. In the Upwelling research prototype, operations are collected into the CRDT, both to represent the actual document and the blame map. A thorough understanding of position mapping in both Slate and automerge will be needed.

Figures (files) cannot be stored in automerge and will be stored separately in **IndexedDB**.

#### Background reading

https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API

https://www.inkandswitch.com/upwelling/

https://www.inkandswitch.com/peritext/

#### Prototyping

To get the editor working, we will store Figures and Documents in PouchDB, a local database that stores binary data. The project names will have to be listed in LocalStorage, and citation information will also be placed in LocalStorage (keyed by source:uid).

## Development plan

Organized into sprints of 80-100 hrs each. RTEs tend to contain a lot of 'last mile' problems, so to produce full functional software as quickly as possible, we will leave out certain configurations of certain elements that will present longer-term problems.

### Sprint 1: Editor

#### Elements handled

Abstract
Bold
Semibold
Italics
Strikethrough
Subscript
Superscript
Paragraphs
Blockquote
Inline code
Code blocks (no syntax highlighting)
Lists (no nested lists)
Headings
Horizontal rule
Links
Equations/LaTeX
Figures
Tables (no tables with joined cells)
Citations (imported from PubMed only)
Special character picker

#### Stories

1. As an author, I need a document editor that handles the above elements, so that I can write my manuscript.
2. As an author, I need a title editor that handles only marks and special characters, so that I can give my project a title.
3. As an author, I need a dialogue to edit author and affiliation metadata, so that I can add correct attribution.
4. As an author, I need to be able to add figures (with captions), and refer to them from within the text of the document, so that I can write my manuscript.
5. As an author, I need to be able to add tables (with captions), and refer to them from within the text of the document, so that I can write my manuscript.
6. As an author, I need to be able to import citations from PubMed, so that I can write my manuscript.
7. As an author, I need to be able to create several projects within the editor, and switch between them, so that I can work on more than one project.
8. As an author, I need to import and deserialize markdown, so that I can work on existing projects.
9. As an author, I need to export to markdown, so that I can manually compile my manuscript in manubot.

### Sprint 2: User, Project, Invitation management

1. As an unauthenticated user, I need to create an account and sync my existing projects, so that unlock collaborative super-powers
2. As an unauthenticated user, I need to sign-in to an existing account and push my existing projects there, so I can work on more than one machine
3. As a user, I need to be able to invite other users to my project, so that I can collaborate with my coworkers.
4. As a user, I need to be able to create new projects on the server, so that I can work on more than one thing
5. As a user, I need to be able to create standard author data, so that it can be applied to each new project I create.
6. As a user, I need to be able to change my password, so that I can protect my account.

### Sprint 3: Creation of manubot(web)

1. As a user, I need to be able to compile my manuscript to PDF, HTML, or JATs, so that I can submit it.

### Sprint 4: Collaboration and syncing 1: collection of operation in automerge

In this sprint, all changes will be collected in automerge and automatically merged into a single document, regardless of who is working. We will also create a blame map (but not render it).

### Sprint 5: Collaboration and syncing 2: Upwelling implementation

In this sprint, we will separate edits into branches with commit messages. We will render the blame map, and create a simple interface for commenting on branches.

### Sprint 6: Complex editor functionality

1. As a user, I need to create nested lists, tables with joined cells and citations from crossref, so that I can correctly edit my manuscript for manubot
2. As a user, I need to be able to authenticate and import author information from ORCID, so that I don't have to do the same work twice.

## Business plan

- Offline use of the editor import/export to markdown will be free
- Collaboration, syncing, other exports will require a subscription