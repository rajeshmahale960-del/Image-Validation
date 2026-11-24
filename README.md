# Precheck Images — Usage Guide

The **Precheck Images** section prepares and organizes image batches before validation using two connected tools: **Download** and **Standardize**.

---

## 1. Downloading Images

Open **Download** (`Download Data Editor`) from **Precheck Images** to fetch images in bulk from links and group them by ID.

### Supported Link Types and Image Link Types

All links must start with `http://` or `https://`.

- **Google Drive File Links** (`drive.google.com`)
  - Standard share links such as `https://drive.google.com/file/d/<FILE_ID>/view` or `?id=<FILE_ID>` (with optional `resourcekey`).
- **Google Drive Folder Links** (`drive.google.com`)
  - Folder share links such as `https://drive.google.com/folders/<FOLDER_ID>` (with optional `resourcekey`), including files inside nested Drive subfolders.
- **Direct / CDN Image Links**
  - Direct `http://` or `https://` image URLs outside Google Drive.
  - Only valid image payloads are accepted; HTML pages, text/JSON responses, and broken/404 links are marked **`Invalid`**.
  - Supported image types include `.jpg`, `.jpeg`, `.png`, `.webp`, `.gif`, `.svg`, `.bmp`, `.avif`, and `.ico` (verified by image content type or file signature).

### Permissions on Google Drive

- Any Google Drive file or folder link **must** be shared as **"Anyone with the link"** (Viewer permission).
- If a Drive link is private or requires sign-in, its **Status** column displays **`Update access`** and the editor shows:
  > *"Please Share the File/Folder to Anyone with Link"*
- **Retry behavior:**
  - Temporary timeouts or transient network errors are retried automatically in the background.
  - Links marked **`Update access`** or **`Invalid`** are not auto-retried. Once you update sharing permissions on Drive, click **Retry** to re-download only the failed rows into the same batch folder.

### How Horizontal Data Pastes to Vertical

You can copy horizontal rows directly from Excel, Google Sheets, or CSV/TSV text and paste (`Ctrl+V`) anywhere inside **Download Data Editor**:

- For every pasted row, the **first cell** is treated as the **ID** (`Column A`).
- Every cell after the first column (`Column 2..N`)—as well as multiple links separated by commas (`,`), semicolons (`;`), or line breaks inside a cell (or space-separated values in a single-cell paste such as `ABC link1,link2`)—is automatically expanded **vertically** into individual `[ID, File/Folder link]` rows.
- Links are always placed strictly into **File/Folder link** (`Column B`), leaving **Status** (`Column C`) blank until **Process** runs.

**Example:**

Pasted horizontal row:
```text
ABC    https://example.com/1.jpg    https://example.com/2.jpg    https://drive.google.com/file/d/...
```

Converted vertical table rows:
| ID | File/Folder link | Status |
| :--- | :--- | :--- |
| ABC | https://example.com/1.jpg | |
| ABC | https://example.com/2.jpg | |
| ABC | https://drive.google.com/file/d/... | |

### How It Shares the Final Folder

- During **Process**, downloaded images are organized into ID-named subfolders inside a temporary session folder (individual file/CDN links under an ID are named sequentially `A`, `B`, ..., `Z`, `AA`, `AB`, ..., while Drive folders keep their original file names).
- Click **Standardize** directly inside **Download Data Editor** once downloads finish:
  1. All unique IDs from **Column A** are validated and transferred automatically into **Standardizer**.
  2. The downloaded session folder is handed off directly to **Standardizer**, which runs automatically and writes the final standardized output into your system **Downloads** folder.
- Closing **Download Data Editor** before clicking **Standardize** prompts for confirmation and cleans up the temporary downloaded files.

---

## 2. Standardizer

Open **Standardize** from **Precheck Images** (or hand off directly from **Download Data Editor**) to organize raw archives or downloaded folders into a clean ID-based directory structure.

### Purpose

**Optimizing and Standardizing Images folders** into a uniform, ID-grouped structure ready for validation.

### Supported Folder and File Types

- Supports uploading one or more **`.zip`** files (via drag-and-drop or **browse**) as well as folders passed directly from **Downloading Images**.
- Scans **nested folders and files up to 25 levels deep** inside the provided archives or folders.

### Images Only

- **Any image format** is supported and included in the standardized output; non-image files are excluded.

### ID Matching and Sequence Suffix Grouping

Enter or paste your target IDs in **List of IDs** (one ID per line).

- Each given ID must match the **Image Name** with an optional **Sequence number suffix**.
- All matching variants for an ID are grouped into that ID's standardized folder.
  - **Example:** Images named `ABC_1`, `ABC`, and `ABC_3` are all grouped into folder **`ABC`**.
- **ID Naming Rules:** IDs cannot contain commas (`,`), parentheses `()`, a trailing fullstop (`.`), Windows-forbidden folder characters (`< > : " / \ | ? *`), or reserved system names (`CON`, `PRN`, `AUX`, `NUL`, `COM1`–`COM9`, `LPT1`–`LPT9`).
- **Output:**
  - The final standardized directory is saved to your **Downloads** folder and can be opened directly via **Open Folder**.
  - If any IDs encounter issues, a `report.csv` (`ID,Issue`) is created inside the output folder alongside a `NOTOK` subfolder containing those ID folders.
