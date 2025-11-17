# TIL: Vercel Python Environment Management


Spent hours debugging `ModuleNotFoundError: No module named 'flask'` on Vercel even though I had `requirements.txt`.

## The Problem

**Vercel reads package files in this priority order:**
1. `uv.lock` (uses uv package manager)
2. `pyproject.toml` (uses Poetry/pip)
3. `requirements.txt` (uses pip)

If it finds `uv.lock`, it ignores everything else.

## The Fix

```bash
# Delete these files
rm uv.lock
rm .python-version
rm -rf .vercel

# Use ONLY this
echo "flask" > requirements.txt
```

**Create `.vercelignore`:**
```
uv.lock
.python-version
pyproject.toml
```


> The `.vercelignore` doesnt seem to actually ignore them while deploying. Hence the file had to be deleted. 

## The Lesson

**For Vercel Python deployments:**
- ✅ Use `requirements.txt` ONLY
- ❌ No `uv.lock`, `pyproject.toml`, or `.python-version`
- ⚠️ Libraries needing system deps (WeasyPrint, OpenCV, etc.) won't work
- 💡 Wrap optional imports in try-catch blocks

## One-Liner Rule

> **"If it's not in `requirements.txt` and doesn't exist on AWS Lambda, it won't work on Vercel."**
