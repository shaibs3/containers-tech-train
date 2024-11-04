Exercise 1: Exploring Basic Overlay Structure
Objective: Set up an overlay filesystem with lower, upper, and merged directories to understand how they interact.

Prepare Directories:

Create a working directory and set up three subdirectories within it: lower, upper, and merged.
bash
Copy code
mkdir -p ~/overlay-lab/{lower,upper,merged}
Add Files to the Lower Directory:

Populate the lower directory with some files.
bash
Copy code
echo "File in lower directory" > ~/overlay-lab/lower/file1.txt
echo "Another file in lower directory" > ~/overlay-lab/lower/file2.txt
Mount the Overlay Filesystem:

Mount an overlay filesystem, using the lower, upper, and merged directories as the lower, upper, and merge layers.
bash
Copy code
sudo mount -t overlay overlay -o lowerdir=~/overlay-lab/lower,upperdir=~/overlay-lab/upper,workdir=~/overlay-lab/upper ~/overlay-lab/merged
Explore the Merged Directory:

List the contents of the merged directory.
bash
Copy code
ls ~/overlay-lab/merged
Observe that file1.txt and file2.txt are visible in the merged directory, demonstrating that the contents of the lower directory appear in the merged view.
Exercise 2: Modifying Files and Observing Changes
Objective: Modify files in the merged directory and observe how changes appear in the upper directory, demonstrating the union of layers.

Modify an Existing File in the Merged Directory:

Edit file1.txt within the merged directory.
bash
Copy code
echo "Modified content in merged directory" >> ~/overlay-lab/merged/file1.txt
Observe Changes in the Upper Directory:

Check the upper directory to see if a new version of file1.txt has been created.
bash
Copy code
ls ~/overlay-lab/upper
cat ~/overlay-lab/upper/file1.txt
Observe that the modification was recorded in the upper directory without altering the original file in the lower directory.
Create a New File in the Merged Directory:

Add a new file directly in the merged directory.
bash
Copy code
echo "New file in merged directory" > ~/overlay-lab/merged/file3.txt
Verify the New File in the Upper Directory:

Confirm that file3.txt only exists in the upper directory, as it was created after the overlay mount.
bash
Copy code
ls ~/overlay-lab/upper
Exercise 3: Deleting Files and Understanding Overlay Behavior
Objective: Delete a file from the merged directory and see how the overlay filesystem handles the deletion while preserving data in the lower layer.

Delete a File from the Merged Directory:

In the merged directory, delete file2.txt.
bash
Copy code
rm ~/overlay-lab/merged/file2.txt
Check the Upper Directory for Deletion Markers:

View the contents of the upper directory to see if a "whiteout" marker (a hidden file) was created to mask file2.txt from the lower layer.
bash
Copy code
ls -a ~/overlay-lab/upper
Verify the Lower Directory Remains Unchanged:

Confirm that file2.txt is still intact in the lower directory.
bash
Copy code
ls ~/overlay-lab/lower
List the Merged Directory Contents:

Observe the contents of the merged directory. file2.txt should not appear, demonstrating that the overlay filesystem hides it without altering the lower layer.
bash
Copy code
ls ~/overlay-lab/merged