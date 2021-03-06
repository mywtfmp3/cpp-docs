---
title: "_matherr | Microsoft Docs"
ms.custom: ""
ms.date: "11/04/2016"
ms.reviewer: ""
ms.suite: ""
ms.technology: ["cpp-standard-libraries"]
ms.tgt_pltfrm: ""
ms.topic: "article"
apiname: ["_matherr"]
apilocation: ["msvcrt.dll", "msvcr80.dll", "msvcr90.dll", "msvcr100.dll", "msvcr100_clr0400.dll", "msvcr110.dll", "msvcr110_clr0400.dll", "msvcr120.dll", "msvcr120_clr0400.dll", "ucrtbase.dll"]
apitype: "DLLExport"
f1_keywords: ["_matherr", "matherr"]
dev_langs: ["C++"]
helpviewer_keywords: ["_matherr function", "matherr function"]
ms.assetid: b600d66e-165a-4608-a856-8fb418d46760
caps.latest.revision: 11
author: "corob-msft"
ms.author: "corob"
manager: "ghogen"
ms.workload: ["cplusplus"]
---
# _matherr
Handles math errors.  
  
## Syntax  
  
```  
  
      int _matherr(  
   struct _exception *except   
);  
```  
  
#### Parameters  
 *except*  
 Pointer to the structure containing error information.  
  
## Return Value  
 _**matherr** returns 0 to indicate an error or a nonzero value to indicate success. If \_**matherr** returns 0, an error message can be displayed and `errno` is set to an appropriate error value. If \_**matherr** returns a nonzero value, no error message is displayed and `errno` remains unchanged.  
  
 For more information about return codes, see [_doserrno, errno, _sys_errlist, and _sys_nerr](../../c-runtime-library/errno-doserrno-sys-errlist-and-sys-nerr.md).  
  
## Remarks  
 The _**matherr** function processes errors generated by the floating-point functions of the math library. These functions call \_**matherr** when an error is detected.  
  
 For special error handling, you can provide a different definition of _**matherr**. If you use the dynamically linked version of the C run-time library (Msvcr90.dll), you can replace the default \_**matherr** routine in a client executable with a user-defined version. However, you cannot replace the default `_matherr` routine in a DLL client of Msvcr90.dll.  
  
 When an error occurs in a math routine, _**matherr** is called with a pointer to an **_exception** type structure (defined in Math.h) as an argument. The **_exception** structure contains the following elements.  
  
 **int type**  
 Exception type.  
  
 **char \*name**  
 Name of the function where error occurred.  
  
 **double arg1**, **arg2**  
 First and second (if any) arguments to the function.  
  
 **double retval**  
 Value to be returned by the function.  
  
 The **type** specifies the type of math error. It is one of the following values, defined in Math.h.  
  
 `_DOMAIN`  
 Argument domain error.  
  
 `_SING`  
 Argument singularity.  
  
 `_OVERFLOW`  
 Overflow range error.  
  
 `_PLOSS`  
 Partial loss of significance.  
  
 `_TLOSS`  
 Total loss of significance.  
  
 `_UNDERFLOW`  
 The result is too small to be represented. (This condition is not currently supported.)  
  
 The structure member **name** is a pointer to a null-terminated string containing the name of the function that caused the error. The structure members **arg1** and **arg2** specify the values that caused the error. (If only one argument is given, it is stored in **arg1**.)  
  
 The default return value for the given error is **retval**. If you change the return value, it must specify whether an error actually occurred.  
  
## Requirements  
  
|Routine|Required header|  
|-------------|---------------------|  
|`_matherr`|\<math.h>|  
  
 For more compatibility information, see [Compatibility](../../c-runtime-library/compatibility.md) in the Introduction.  
  
## Libraries  
 All versions of the [C run-time libraries](../../c-runtime-library/crt-library-features.md).  
  
## Example  
  
```  
// crt_matherr.c  
/* illustrates writing an error routine for math   
 * functions. The error function must be:  
 *      _matherr  
 */  
  
#include <math.h>  
#include <string.h>  
#include <stdio.h>  
  
int main()  
{  
    /* Do several math operations that cause errors. The _matherr  
     * routine handles _DOMAIN errors, but lets the system handle  
     * other errors normally.  
     */  
    printf( "log( -2.0 ) = %e\n", log( -2.0 ) );  
    printf( "log10( -5.0 ) = %e\n", log10( -5.0 ) );  
    printf( "log( 0.0 ) = %e\n", log( 0.0 ) );  
}  
  
/* Handle several math errors caused by passing a negative argument  
 * to log or log10 (_DOMAIN errors). When this happens, _matherr  
 * returns the natural or base-10 logarithm of the absolute value  
 * of the argument and suppresses the usual error message.  
 */  
int _matherr( struct _exception *except )  
{  
    /* Handle _DOMAIN errors for log or log10. */  
    if( except->type == _DOMAIN )  
    {  
        if( strcmp( except->name, "log" ) == 0 )  
        {  
            except->retval = log( -(except->arg1) );  
            printf( "Special: using absolute value: %s: _DOMAIN "  
                     "error\n", except->name );  
            return 1;  
        }  
        else if( strcmp( except->name, "log10" ) == 0 )  
        {  
            except->retval = log10( -(except->arg1) );  
            printf( "Special: using absolute value: %s: _DOMAIN "  
                     "error\n", except->name );  
            return 1;  
        }  
    }  
    printf( "Normal: " );  
    return 0;    /* Else use the default actions */  
}  
```  
  
## Output  
  
```  
Special: using absolute value: log: _DOMAIN error  
log( -2.0 ) = 6.931472e-001  
Special: using absolute value: log10: _DOMAIN error  
log10( -5.0 ) = 6.989700e-001  
Normal: log( 0.0 ) = -1.#INF00e+000  
```  
  
## See Also  
 [Floating-Point Support](../../c-runtime-library/floating-point-support.md)   