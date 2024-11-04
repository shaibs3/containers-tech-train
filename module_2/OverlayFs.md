
# Overlay Filesystem Lab

In this lab, you will explore the basic concepts of overlay filesystems, specifically focusing on the lower, upper, and merged directories. You will learn how files are layered, modified, and merged.

## Setup
Start by creating a directory structure for the exercises:
```bash
mkdir -p ~/overlay-lab/{lower,upper,merged}
```

---

## Exercise 1: Exploring Basic Overlay Structure

**Objective:** Set up an overlay filesystem with `lower`, `upper`, and `merged` directories to understand how they interact.

1. **Add Files to the Lower Directory:**
   ```bash
   echo "File in lower directory" > ~/overlay-lab/lower/file1.txt
   echo "Another file in lower directory" > ~/overlay-lab/lower/file2.txt
   ```

2. **Mount the Overlay Filesystem:**
   ```bash
   sudo mount -t overlay overlay -o lowerdir=~/overlay-lab/lower,upperdir=~/overlay-lab/upper,workdir=~/overlay-lab/upper ~/overlay-lab/merged
   ```

3. **Explore the Merged Directory:**
   ```bash
   ls ~/overlay-lab/merged
   ```
   You should see `file1.txt` and `file2.txt`, demonstrating that files in the lower directory appear in the merged view.

---

## Exercise 2: Modifying Files and Observing Changes

**Objective:** Modify files in the merged directory and observe how changes appear in the upper directory.

1. **Modify an Existing File in the Merged Directory:**
   ```bash
   echo "Modified content in merged directory" >> ~/overlay-lab/merged/file1.txt
   ```

2. **Observe Changes in the Upper Directory:**
   ```bash
   ls ~/overlay-lab/upper
   cat ~/overlay-lab/upper/file1.txt
   ```
   You should see a new version of `file1.txt` in the upper directory, containing your changes.

3. **Create a New File in the Merged Directory:**
   ```bash
   echo "New file in merged directory" > ~/overlay-lab/merged/file3.txt
   ```

4. **Verify the New File in the Upper Directory:**
   ```bash
   ls ~/overlay-lab/upper
   ```
   The new file `file3.txt` will only appear in the upper directory, as it was created after the overlay mount.

---

## Exercise 3: Deleting Files and Understanding Overlay Behavior

**Objective:** Delete a file from the merged directory and see how the overlay filesystem handles deletions.

1. **Delete a File from the Merged Directory:**
   ```bash
   rm ~/overlay-lab/merged/file2.txt
   ```

2. **Check the Upper Directory for Deletion Markers:**
   ```bash
   ls -a ~/overlay-lab/upper
   ```
   A "whiteout" marker should appear, hiding `file2.txt` from the lower layer without deleting it.

3. **Verify the Lower Directory Remains Unchanged:**
   ```bash
   ls ~/overlay-lab/lower
   ```
   The original `file2.txt` should still be in the lower directory.

4. **List the Merged Directory Contents:**
   ```bash
   ls ~/overlay-lab/merged
   ```
   The merged directory should no longer show `file2.txt`, as it’s hidden by the overlay filesystem.

---

After completing these exercises, you should have a better understanding of how the overlay filesystem uses lower, upper, and merged directories to manage data layers.
