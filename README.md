# Permissions
Visual Flowchart: Step-by-step breakdown of Linux permission structure (owner/group/other with read/write/execute)
Permission Analysis: Converting rwxrwxr-x to octal format (775)
Python Implementation: Two methods for applying chmod - native os.chmod() and subprocess system calls

# Flowchart
<img width="1278" height="1035" alt="Image" src="https://github.com/user-attachments/assets/7f0a534c-d96f-4cc1-af6b-392284f0f8f8" />

# Code

’’’ import os
import stat
import subprocess
import sys
from pathlib import Path

def explain_permissions():
    """Explain Linux file permissions structure"""
    print("=" * 50)
    print("LINUX FILE PERMISSIONS EXPLAINED")
    print("=" * 50)
    print("Permission format: rwxrwxr-x")
    print("Position breakdown:")
    print("  1-3: Owner permissions  (rwx)")
    print("  4-6: Group permissions  (rwx)")
    print("  7-9: Other permissions  (r-x)")
    print()
    print("Permission meanings:")
    print("  r = Read    (4)")
    print("  w = Write   (2)")
    print("  x = Execute (1)")
    print()
    print("Target permissions: rwxrwxr-x")
    print("  Owner: rwx = 4+2+1 = 7")
    print("  Group: rwx = 4+2+1 = 7")
    print("  Other: r-x = 4+0+1 = 5")
    print("  Octal: 775")
    print("=" * 50)

def create_test_file(filename="test_file.txt"):
    """Create a test file for permission demonstration"""
    filepath = Path(filename)
    try:
        with open(filepath, 'w') as f:
            f.write("This is a test file for chmod demonstration.\n")
        print(f"✓ Created test file: {filepath}")
        return filepath
    except Exception as e:
        print(f"✗ Error creating file: {e}")
        return None

def get_current_permissions(filepath):
    """Get current file permissions"""
    try:
        file_stat = filepath.stat()
        mode = file_stat.st_mode
        
        # Convert to octal string (remove '0o' prefix)
        octal_perms = oct(stat.S_IMODE(mode))[2:]
        
        # Convert to readable format
        readable_perms = stat.filemode(mode)[1:]  # Remove first character (file type)
        
        return octal_perms, readable_perms
    except Exception as e:
        print(f"✗ Error getting permissions: {e}")
        return None, None

def set_permissions_python(filepath, mode=0o775):
    """Set file permissions using Python's os.chmod()"""
    try:
        print(f"\nSetting permissions using Python os.chmod()...")
        os.chmod(filepath, mode)
        print(f"✓ Permissions set to {oct(mode)[2:]} using Python")
        return True
    except Exception as e:
        print(f"✗ Error setting permissions with Python: {e}")
        return False

def set_permissions_subprocess(filepath, mode="775"):
    """Set file permissions using subprocess to call system chmod"""
    try:
        print(f"\nSetting permissions using subprocess (system chmod)...")
        result = subprocess.run(['chmod', mode, str(filepath)], 
                              capture_output=True, text=True)
        if result.returncode == 0:
            print(f"✓ Permissions set to {mode} using system chmod")
            return True
        else:
            print(f"✗ Error with system chmod: {result.stderr}")
            return False
    except Exception as e:
        print(f"✗ Error calling system chmod: {e}")
        return False

def demonstrate_permissions():
    """Main demonstration function"""
    explain_permissions()
    
    # Create test file
    test_file = create_test_file()
    if not test_file:
        return
    
    # Show initial permissions
    print(f"\nInitial permissions:")
    octal, readable = get_current_permissions(test_file)
    if octal and readable:
        print(f"  Octal: {octal}")
        print(f"  Readable: {readable}")
    
    # Method 1: Using Python's os.chmod()
    if set_permissions_python(test_file, 0o775):
        octal, readable = get_current_permissions(test_file)
        print(f"  New permissions - Octal: {octal}, Readable: {readable}")
    
    # Reset permissions for next demo
    os.chmod(test_file, 0o644)
    
    # Method 2: Using subprocess to call system chmod
    if set_permissions_subprocess(test_file, "775"):
        octal, readable = get_current_permissions(test_file)
        print(f"  New permissions - Octal: {octal}, Readable: {readable}")
    
    print(f"\nPermission verification:")
    final_octal, final_readable = get_current_permissions(test_file)
    if final_readable == "rwxrwxr-x":
        print("✓ SUCCESS: Target permissions rwxrwxr-x achieved!")
    else:
        print(f"✗ Current permissions: {final_readable}")
    
    # Cleanup
    print(f"\nCleaning up test file...")
    try:
        test_file.unlink()
        print("✓ Test file removed")
    except Exception as e:
        print(f"✗ Error removing file: {e}")

def permission_calculator():
    """Interactive permission calculator"""
    print("\n" + "=" * 50)
    print("PERMISSION CALCULATOR")
    print("=" * 50)
    
    print("Enter desired permissions for each group (y/n):")
    
    # Owner permissions
    print("\nOwner permissions:")
    owner_r = input("  Read (r)? [y/n]: ").lower().startswith('y')
    owner_w = input("  Write (w)? [y/n]: ").lower().startswith('y')
    owner_x = input("  Execute (x)? [y/n]: ").lower().startswith('y')
    
    # Group permissions
    print("\nGroup permissions:")
    group_r = input("  Read (r)? [y/n]: ").lower().startswith('y')
    group_w = input("  Write (w)? [y/n]: ").lower().startswith('y')
    group_x = input("  Execute (x)? [y/n]: ").lower().startswith('y')
    
    # Other permissions
    print("\nOther permissions:")
    other_r = input("  Read (r)? [y/n]: ").lower().startswith('y')
    other_w = input("  Write (w)? [y/n]: ").lower().startswith('y')
    other_x = input("  Execute (x)? [y/n]: ").lower().startswith('y')
    
    # Calculate octal values
    owner_octal = (4 if owner_r else 0) + (2 if owner_w else 0) + (1 if owner_x else 0)
    group_octal = (4 if group_r else 0) + (2 if group_w else 0) + (1 if group_x else 0)
    other_octal = (4 if other_r else 0) + (2 if other_w else 0) + (1 if other_x else 0)
    
    # Build readable format
    owner_str = ('r' if owner_r else '-') + ('w' if owner_w else '-') + ('x' if owner_x else '-')
    group_str = ('r' if group_r else '-') + ('w' if group_w else '-') + ('x' if group_x else '-')
    other_str = ('r' if other_r else '-') + ('w' if other_w else '-') + ('x' if other_x else '-')
    
    readable = owner_str + group_str + other_str
    octal = f"{owner_octal}{group_octal}{other_octal}"
    
    print(f"\nCalculated permissions:")
    print(f"  Readable format: {readable}")
    print(f"  Octal format: {octal}")
    print(f"  Python chmod: os.chmod(filepath, 0o{octal})")
    print(f"  System chmod: chmod {octal} filename")

if __name__ == "__main__":
    print("Linux File Permissions with Python")
    print("Choose an option:")
    print("1. Run permission demonstration")
    print("2. Use permission calculator")
    print("3. Both")
    
    try:
        choice = input("\nEnter choice (1-3): ").strip()
        
        if choice in ['1', '3']:
            demonstrate_permissions()
        
        if choice in ['2', '3']:
            permission_calculator()
            
        if choice not in ['1', '2', '3']:
            print("Invalid choice. Running full demonstration...")
            demonstrate_permissions()
            
    except KeyboardInterrupt:
        print("\n\nProgram interrupted by user.")
    except Exception as e:
        print(f"\nUnexpected error: {e}")
