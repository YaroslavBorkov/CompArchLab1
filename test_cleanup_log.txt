  GNU nano 7.2                                test_cleanup_log.sh                                        

TEST_DIR="./test_log"
BACKUP_DIR="./backup"
THRESHOLD=70
NUM_FILES=5

setup_test() {
    rm -rf "$TEST_DIR" "$BACKUP_DIR"
    mkdir -p "$TEST_DIR"
    for i in {1..10}; do
        dd if=/dev/zero of="$TEST_DIR/file$i.log" bs=1M count=100 status=none
        sleep 0.1
    done
}

run_test() {
    echo "Running $1..."
    setup_test
    ./cleanup_log.sh "$TEST_DIR" "$THRESHOLD" "$NUM_FILES"
    echo "Test $1 completed."
    echo
}

run_test "Threshold Exceeded"
run_test "Below Threshold" 
run_test "Below Threshold"
run_test "Oldest Files Archived"
echo "Test 4: Non-existent directory..."
./cleanup_log.sh "/nonexistent" 70 5
echo
echo "All tests completed."
