GNU nano 7.2                                         cleanup_log.sh                                                  BACKUP_DIR="$HOME/backup

LOG_DIR="$1"
BACKUP_DIR="$HOME/backup" 
THRESHOLD="$2"
NUM_FILES="$3"

if [ -z "$LOG_DIR" ] [ -z "$NUM_FILES" ]; then
    echo "Usage: $0 <log_directory> <threshold_percent> <num_files_to_archive>"
    exit 1
fi

if [ ! -d "$LOG_DIR" ]; then
    echo "Error: Directory $LOG_DIR does not exist."
    exit 1
fi

USAGE_PERCENT=$(df --output=pcent "$LOG_DIR" | tail -1 | tr -d '% ')

if [ "$USAGE_PERCENT" -gt "$THRESHOLD" ]; then
    echo "Usage ($USAGE_PERCENT%) exceeds threshold ($THRESHOLD%). Archiving $NUM_FILES oldest files..."

    mkdir -p "$BACKUP_DIR"

    find "$LOG_DIR" -maxdepth 1 -type f -printf "%T@ %p\n" | sort -n | head -n "$NUM_FILES" | cut -d' ' -f2- | \
    while read -r file; do
        echo "Archiving $file"
        tar -czf "$BACKUP_DIR/$(basename "$file").tar.gz" -C "$LOG_DIR" "$(basename "$file")" && rm "$file"
    done
    echo "Archiving complete."
else
    echo "Usage ($USAGE_PERCENT%) is below threshold ($THRESHOLD%). No action needed."
fi
