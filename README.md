# Oracle Data Generator

The purpose of this project is to simulate Oracle Audit Events gathered from a sample set gathered from multiple public sources. The generator interprets an audit event and converts to either a JSON or XML encoded message.

For instance, we are given an audit record in the Oracle log as follows:

```bash
TIMESTAMP: "2024-09-09 10:25:00"
SESSIONID: "100003"
ENTRYID: "1"
USERID: "UNKNOWN"
ACTION: "INSERT"
RETURNCODE: "1017"
COMMENT$TEXT: "ORA-01017: invalid username/password; logon denied"
OBJECT_SCHEMA: "HR"
OBJECT_NAME: "EMPLOYEES"
SQL_TEXT: "INSERT INTO HR.EMPLOYEES (EMPLOYEE_ID, NAME, SALARY) VALUES (1002, 'Jane Doe', 4500)"
```

The data generator reads the original item and formats the message as follows:

```xml
<AuditRecord>
    <Timestamp>2024-09-12T12:11:27.435741-04:00</Timestamp>
    <Sessionid>100003</Sessionid>
    <Entryid>1</Entryid>
    <Userid>UNKNOWN</Userid>
    <Action>INSERT</Action>
    <Returncode>1017</Returncode>
    <Comment_Text>ORA-01017: invalid username/password; logon denied</Comment_Text>
    <Object_Schema>HR</Object_Schema>
    <Object_Name>EMPLOYEES</Object_Name>
    <Sql_Text>INSERT INTO HR.EMPLOYEES (EMPLOYEE_ID, NAME, SALARY) VALUES (1002, &amp;apos;Jane Doe&amp;apos;, 4500)</Sql_Text>
</AuditRecord>
```

In the end, the data is sent to a SentinelOne raw ingestion API endpoint for processing. The intent is for the message to flow into the Singularity Data Lake and interpreted by the Singularity AI SIEM.

![screenshot](img/screenshot-01.png)

# Data Parser

In our generation process, we categorize the data with the `oracle_audit` source type. This is an arbitrary convention and you can modify as you see fit. To process the ingestion and interpret the meta data, we prepared a cursory [data parser](parser/oracle_audit_parser.json).

The parser extracts the XML keys and their corresponding values. These automated extractions emphasize search and analysis with the Singularity AI SIEM. For instance, the parser extracts the XML keys and values as follows:

| Field | Value |
------- | --------
| Action | INSERT |
| Comment_Text | ORA-01017: invalid username |
| Entryid | 1 |
| Object_Name | EMPLOYEES | 
| Object_Schema | HR
| Returncode | 1017 | 
| Sessionid | 100003 | 
| Sql_Text | INSERT INTO HR.EMPLOYEES (EMPLOYEE_ID, NAME, SALARY) VALUES (1002, &amp;apos;Jane Doe&amp;apos;, 4500) | 
| Timestamp | 2024-09-12T12:11:27.435741-04:00 | 
| Userid | UNKNOWN | 
| dataSource.category | Audit | 
| dataSource.name | Oracle Database | 
| dataSource.vendor | Oracle Datagen | 
| dataset | oracle_audit | 
