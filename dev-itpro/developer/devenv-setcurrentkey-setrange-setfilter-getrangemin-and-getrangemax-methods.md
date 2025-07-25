---
title: Filtering records with the SetRange, SetFilter, GetRangeMin, and GetRangeMax methods
description: Describes methods that set limits on the value of one or more specified fields, so that only a subset of the records are displayed, modified, deleted, and so on. 
ms.custom: evergreen
ms.date: 04/18/2024
ms.update-cycle: 1095-days
ms.topic: concept-article
author: SusanneWindfeldPedersen
---

# Filtering records with the SetRange, SetFilter, GetRangeMin, and GetRangeMax methods

The following methods can be used to filter records in a table:  

- [SetRange](#setrange-method)  
- [SetFilter](#setfilter-method)  
- [GetRangeMin](#getrangemin-method)  
- [GetRangeMax](#getrangemax-method)  

These methods are some of the most commonly used AL methods. They set limits on the value of one or more specified fields, so that only a subset of the records are displayed, modified, deleted, and so on.  

## SetRange method

`SetRange` sets a simple filter on a field.  

`SetRange` has the following syntax.  

```AL
Record.SetRange(Field [,From-Value] [,To-Value]);  
```  

In the following example, `SetRange` filters the **Customer** table by selecting only those records where the **No.** field has a value between 10000 and 90000.  

```AL
Customer.SetRange("No.",'10000','90000');  
```  

When you use `SetRange` the following rules apply:  

- `SetRange` removes any filters that were set previously and replaces them with the *From-Value* and *To-Value* parameters that you specify.  
- If you use `SetRange` without setting the *From-Value* and *To-Value* parameters, the method removes any filters that are already set.  
- If you only set the *From-Value*, the *To-Value* is set to the same value as the *From-Value*.  

## SetFilter method

`SetFilter` sets a filter in a more general way than `SetRange`.  

`SetFilter` has the following syntax.  

```AL
Record.SetFilter(Field, String [, Value], ...]);  
```  

*Field* is the name of the field on which you want to set a filter. *String* is the filter expression. *String* may contain placeholders, such as %1 and %2, to indicate where to insert the *Value* parameter\(s\) in a filter expression.  

The following example selects records where the value of No. is larger than 10000 and not equal to 20000.  

```AL
Customer.SetFilter("No.", '>10000 & <> 20000');  
```  

If the variables *Value1* and *Value2* have been assigned "10000" and "20000" respectively, then you can use the following statement to create the same filter.  

```AL 
Customer.SetFilter("No.",'>%1&<>%2',Value1, Value2);  
```  

## GetRangeMin method  

`GetRangeMin` retrieves the minimum value of the filter range that is currently applied to a field.  

`GetRangeMin` has the following syntax.  

```AL
Record.GetRangeMin(Field);  
```  

A runtime error occurs if the filter that is currently applied is not a range. For example, you can set a filter as follows.  

```AL
Customer.SetFilter("No.",'10000|20000|30000');  
```  

With this filter, the following code fails because the filter isn't a range.  

```AL
BottomValue := Customer.GetRangeMin("No.");  
```  

## GetRangeMax method

`GetRangeMax` retrieves the maximum value of the filter range that is currently applied to a field.  

`GetRangeMax` has the following syntax.  

```AL
Value := Record.GetRangeMax(Field)  
```

## Related information

[SetRange method](methods-auto/record/record-setrange-method.md)  
[SetFilter method](methods-auto/record/record-setfilter-method.md)    
[GetRangeMin method](methods-auto/record/record-getrangemin-method.md)  
[GetRangeMax method](methods-auto/record/record-getrangemax-method.md)  
