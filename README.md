
CODTECH INTERNSHIP - TASK 1
FILE INTEGRITY CHECKER

Author: [Your Name]
Date: [Today's Date]
Description:
This script is designed to monitor changes in a file by generating
its SHA-256 hash and comparing it with a previously stored hash value.
"""

import hashlib
import os

def calculate_hash(filename):
    """
    Calculates the SHA-256 hash of a given file.

    Parameters:
        filename (str): Path to the file.

    Returns:
        str: SHA-256 hash of the file content.
    """
    try:
        hasher = hashlib.sha256()
        with open(filename, 'rb') as f:
            buf = f.read()
            hasher.update(buf)
        return hasher.hexdigest()
    except FileNotFoundError:
        return None

def save_original_hash(filename, hash_value):
    """
    Saves the original hash value to a text file for future comparison.

    Parameters:
        filename (str): File to save hash against.
        hash_value (str): The hash to be saved.
    """
    with open("stored_hash.txt", "w") as f:
        f.write(f"{filename},{hash_value}")
    print( Original hash saved successfully.")

def load_stored_hash():
    """
    Loads the stored hash and file name from the saved file.

    Returns:
        tuple: (filename, stored_hash) or (None, None) if not found.
    """
    if os.path.exists("stored_hash.txt"):
        with open("stored_hash.txt", "r") as f:
            data = f.read().strip().split(",")
            if len(data) == 2:
                return data[0], data[1]
    return None, None

def check_file_integrity(filename):
    """
    Compares the current file hash with the stored hash.

    Parameters:
        filename (str): File to check.

    Returns:
        str: Result message about file integrity.
    """
    original_file, stored_hash = load_stored_hash()
    if filename != original_file:
        return "Stored hash is for a different file. Please re-save the original hash."

    current_hash = calculate_hash(filename)
    if not current_hash:
        return "File not found."

    if current_hash == stored_hash:
        return "File is intact. No changes detected."
    else:
        return "WARNING: File has been modified!"

# -----------------------------
# Example CLI-Based Execution
# -----------------------------
if __name__ == "__main__":
    print("FILE INTEGRITY CHECKER")
    print("1. Save original file hash")
    print("2. Check file integrity")
    choice = input("Enter your choice (1 or 2): ")

    filename = input("Enter the file name (with extension): ")

    if choice == "1":
        hash_value = calculate_hash(filename)
        if hash_value:
            save_original_hash(filename, hash_value)
        else:
            print(" File not found.")
    elif choice == "2":
        result = check_file_integrity(filename)
        print(result)
    else:
        print("Invalid option selected.")
