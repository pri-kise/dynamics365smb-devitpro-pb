---
title: "RecordRef.GetTable(Record) Method"
description: "Gets the table of a Record variable and causes the RecordRef to refer to the same table."
ms.author: solsen
ms.custom: na
ms.date: 07/07/2021
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: reference
author: SusanneWindfeldPedersen
---
[//]: # (START>DO_NOT_EDIT)
[//]: # (IMPORTANT:Do not edit any of the content between here and the END>DO_NOT_EDIT.)
[//]: # (Any modifications should be made in the .xml files in the ModernDev repo.)
# RecordRef.GetTable(Record) Method
> **Version**: _Available or changed with runtime version 1.0._

Gets the table of a Record variable and causes the RecordRef to refer to the same table.


## Syntax
```AL
 RecordRef.GetTable(Rec: Record)
```
## Parameters
*RecordRef*  
&emsp;Type: [RecordRef](recordref-data-type.md)  
An instance of the [RecordRef](recordref-data-type.md) data type.  

*Rec*  
&emsp;Type: [Record](../record/record-data-type.md)  
Use this record variable to specify the table to which the RecordRefVar refers.  



[//]: # (IMPORTANT: END>DO_NOT_EDIT)

## Remarks  
 Any filters that are applied to the *RecordVar* are also applied to the *RecordRefVar*.  
  
 Another way to select the table to which a RecordRef refers is to use the [Open Method \(RecordRef\)](recordref-open-method.md) and specify a table number in the parameters.  
  
## Example  
 The following example is an excerpt from codeunit 8, AccSchedManagement. It iterates through records in the G/L Account table. It sets some values on the fields of a new record in the Acc. Schedule Line table based on the current G/L Account record and inserts the new record into the Acc. Schedule Line table. It calls GetTable to cause a RecordRef variable to refer to the same table as the new Acc. Schedule Line record, and then calls the LogInsertion method from codeunit 423, Change Log Management to log the change. The LogInsertion method requires a RecordRef as a parameter.  
  
 This example assumes that the AccSchedLineNo variable has been assigned a value previously in the code.  

```al
var
    AccSchedLine: Record "Acc. Schedule Line";
    AccSchedLineNo: Integer;
    GLAcc: Record "G/L Account";
    RecRef: RecordRef;
    ChangeLogMgt: Codeunit "Change Log Management";
begin 
    if GLAcc.Find('-') then  
      repeat  
        AccSchedLine.Init;  
        AccSchedLine."Line No." := AccSchedLineNo;  
        AccSchedLineNo := AccSchedLineNo + 10000;  
        AccSchedLine.Description := GLAcc.Name;  
        if GLAcc."Account Type" IN [GLAcc."Account Type"::Posting,GLAcc."Account Type"::Total,GLAcc."Account Type"::"End-Total"] then begin  
          AccSchedLine.Totaling := GLAcc."No.";  
          AccSchedLine."Row No." := CopyStr(GLAcc."No.",1,MaxStrLen(AccSchedLine."Row No."));  
        end;  
        if GLAcc."Account Type" IN [GLAcc."Account Type"::Total,GLAcc."Account Type"::"End-Total"] then  
          AccSchedLine."Totaling Type" := AccSchedLine."Totaling Type"::"Total Accounts"  
        else  
          AccSchedLine."Totaling Type" := AccSchedLine."Totaling Type"::"Posting Accounts";  
        AccSchedLine.Insert;  
        RecRef.GetTable(AccSchedLine);  
        ChangeLogMgt.LogInsertion(RecRef);  
      until GLAcc.Next = 0;  
end;
```  

## See Also
[RecordRef Data Type](recordref-data-type.md)  
[Get Started with AL](../../devenv-get-started.md)  
[Developing Extensions](../../devenv-dev-overview.md)