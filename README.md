# Student Performance CLI

A Java 21 CLI that reads one submission metadata JSON file, loads all Java files for that submission, and reports test outcomes and source findings. Submitted source is parsed for syntax diagnostics and empty catch blocks; it is not compiled or executed.

## Build and run

Build with Maven:

```sh
mvn package
```

Run the packaged CLI:

```sh
java -jar target/student-performance-cli-0.1.0.jar path/to/submission-info.json
```

The process returns `0` after producing a report, `1` for invalid or unreadable submission input, and `2` when invoked with the wrong number of arguments.

## Input layout

The source directory is located beside the submission info JSON. It must contain a directory named with the submission ID. All `.java` files under that directory are loaded recursively and analyzed together.

```text
submission-001.json
source/
  submission-001/
    Main.java
    model/Result.java
```

Example `submission-001.json`:

```json
{
  "submissionId": "submission-001",
  "assignmentId": "assignment-42",
  "studentId": "student-7",
  "submittedAt": "2026-10-14T09:30:00-06:00",
  "attemptNumber": 2,
  "testResults": [
    { "name": "handles empty input", "status": "pass" },
    { "name": "rejects invalid input", "status": "fail", "message": "Expected an error" }
  ]
}
```

Required fields are non-empty string IDs, an ISO 8601 `submittedAt` timestamp with an offset, and a positive integer `attemptNumber`. `testResults` may be omitted; when present, it must be an array of objects with a non-empty `name`, `status` of `pass` or `fail`, and optional string `message`.

Input syntax and required-field validation are performed independently before Gson deserialization. Gson is used to deserialize validated metadata and serialize model records.
