# macdocling

A macOS Quick Action that lets you right-click any document in Finder and convert it to Markdown using [Docling](https://github.com/docling-project/docling).

Supports PDF, Word (.docx), PowerPoint (.pptx), Images, HTML, and more.

![Demo](demo.gif)

## Step 1: Install Docling

```bash
pip install docling
```

Verify it works:

```bash
docling some-file.pdf --to md
```

If `docling` isn't found after install, check where it landed:

```bash
python3 -m site --user-base
```

The binary will be in the `bin/` subdirectory of that path (e.g. `~/Library/Python/3.12/bin/docling`).

## Step 2: Install the Quick Action

You have two options:

### Option A: Install the pre-built workflow

1.  Download `Convert to Markdown.workflow` from this repo (or clone the repo)
    
2.  Double-click `Convert to Markdown.workflow`
    
3.  Click **Install** when prompted
    

Done. Right-click any file in Finder → **Quick Actions** → **Convert to Markdown**.

### Option B: Create your own workflow in Automator

1.  Open **Automator** (`Cmd + Space`, type "Automator")
    
2.  Click **New Document** → choose **Quick Action**
    
3.  At the top, set:
    
    -   **Workflow receives current:** `files or folders`
        
    -   **in:** `Finder`
        
4.  In the left sidebar, search for **Run Shell Script** and drag it to the workflow area
    
5.  Set **Shell** to `/bin/bash` and **Pass input** to `as arguments`
    
6.  Paste this script:
    

```bash
export PATH="/usr/local/bin:/opt/homebrew/bin:$HOME/Library/Python/3.11/bin:$HOME/Library/Python/3.12/bin:$HOME/Library/Python/3.13/bin:$HOME/Library/Python/3.14/bin:$PATH"

# Find docling
for pyver in 14 13 12 11; do
    DOCLING="$HOME/Library/Python/3.$pyver/bin/docling"
    [ -f "$DOCLING" ] && break
done

[ ! -f "$DOCLING" ] && DOCLING="/opt/homebrew/bin/docling"
[ ! -f "$DOCLING" ] && DOCLING="/usr/local/bin/docling"

if [ ! -f "$DOCLING" ]; then
    osascript -e 'display notification "Install docling: pip install docling" with title "Error"'
    exit 1
fi

# Convert files
for f in "$@"; do
    [ -f "$f" ] && "$DOCLING" "$f" --output "$(dirname "$f")" --to md
done

osascript -e 'display notification "Done!" with title "Docling"'
```

7.  Save (`Cmd + S`) with the name **Convert to Markdown**
    

Done. Right-click any file in Finder → **Quick Actions** → **Convert to Markdown**.

## Usage

1.  Select one or more files in Finder
    
2.  Right-click → **Quick Actions** → **Convert to Markdown**
    
3.  A notification appears when conversion finishes
    
4.  The `.md` file is created next to the original
    

## Troubleshooting

**"Install docling" notification appears:**  
Your `docling` binary isn't in any of the expected paths. Run `which docling` in Terminal and make sure it's installed.

**Quick Action doesn't show up in the menu:**  
Go to **System Settings** → **Privacy & Security** → **Extensions** → **Finder** and make sure "Convert to Markdown" is enabled.

**Conversion fails silently:**  
Test the conversion manually in Terminal first: `docling your-file.pdf --to md`. If that fails, the issue is with Docling, not this workflow.
