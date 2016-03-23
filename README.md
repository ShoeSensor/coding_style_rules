# Coding style rules

### 1. Purpose of this document
It's important all code developed by this project group is in the same style. This makes code easier to read and maintain. This document will describe how to comply with the expo 20 coding style.

## 2. General rules
- Use tabs instead of spaces
	- Tabs should be 4 spaces
- Variable names should be like this: `thisIsAnExample`
	- Variable names should always be self explanatory.
- Function names should be like this: {namespace}_functionName
	- The beginning of the function name should always be the name of the library category
	- Example: `os_threadStart()`, os is here the namespace and thread is the category
- If function paramaters are more then 4, use a structure to pass them all at once
- Never exceed **80** spaces in a source file, except for documentation
- Always use the header template
- Functions in libraries should be in natural order
	- eg: Initialization, start, stop, deinitialize
- Brackets are always on the same line, except for functions. For functions start brackets on a new line.
- An keyword like `if` of `while` is followed by only one statement, do not use brackets.
- Do not use `goto`
	- Only use goto for error handling
- Always use doxygen documenting style
- Avoid global variables
- **Always** use datatypes with the number of bits specified e.g. `uint32_t` instead of `int`
	- This improves portability and can reduce memory usage
- The top of a source file contains the license and copyright
- Enum members are all CAPS.
- Types end with {name}_t. Types are variables created with `typedef`

## Documenting code
For documenting code, we use * doxygen*. Doxygen is a tool that creates html pages from documentation.
To write your documentation in doxygen style, follow the following guide:

#### Set eclipse to use doxygen style documentation
Go to **Window -> Preferences -> C/C++ -> Editor -> Documentation tool comments **
Select *Doxygen* in the **workspace default** choicebox.
After this, eclipse automatically generates a part of the doxygen documentation for you.

To do this, put the cursor on the line above a function and type:

`/**` followed by enter.
Now it should look like this:
```language
/**
 * 
 * @param start Start time to use as reference.
 * @return The amount of milliseconds since start.
 */

```
This is almost what we want, to complete it add @brief above @param.
Note that `/**` is very important. The doxygen tool recognizes this as a piece of documentation.

#### Doxygen identifiers

###### File identifiers

Files start with a piece of license followed by a piece of doxygen documentation. This is used by doxygen to distinguish modules.
It should always look like this:
```c
/** @file
 * @defgroup {Module name}
 * @{
 * @ingroup  {Parent module name}
 *
 * @brief {Comment about this module}
 *
 */
```

Files should always end with:

```c
/**
 *@}
 **/
```

###### Function identifiers

`@brief` : Used to give a brief description of the function.

`@details`: Used to give a details description about the function.

`@param x`: Used to give a explain the parameter with name *x*.

`@return`: Used to explain the return value.

`@retval`: Descripe a specific return value e.g. `true` and `false`.

`@note`: Additional about the function.

###### Structures and enums

To document structures and enums, do the following:

```c
typedef struct {
    type member1;               	/**< Description about member1*/
    type member2;                	/**< Description about member2*/
    type member3;                   /**< Description about member3*/
} {struct name}_t;
```
The same goes for enums.
Note that `/**<` is very important.

## Eclipse auto formatter

The eclipse formatter is a great tools and it almost does what we want. To configure the eclipse formatter to our needs, import the configuration file. The configuration file is called `formatter_conf.xml` and is located in this repository.

To import this file go to: ** Window -> Preferences -> Formatter -> Import... ** and choose this file. Downloads the file first if you haven't already.
What this does, it changes tabs to spaces and creates brackets for functions on a new line.


## Examples

The following piece of code is fully in compliance with the coding style described in the document:
```c
/*
 * Copyright 2016 Bart Monhemius.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

/** @file
 * @defgroup os_thread Threading
 * @{
 * @ingroup os
 *
 * @brief FreeRTOS thread abstraction layer
 *
 */

#ifndef OS_THREAD_H
#define OS_THREAD_H

#include <stdbool.h>
#include <stdint.h>
#include <stdlib.h>

#ifdef  __cplusplus
extern "C" {
#endif

typedef void(*os_threadCallback_t)(void*);
typedef struct os_threadHandle *os_threadHandle_t;

typedef enum {
    THREAD_PRIO_LOW = 0,    /**< Lowest thread priority*/
    THREAD_PRIO_NORM,       /**< Normal thread priority*/
    THREAD_PROI_HIGH        /**< Highest thread priority*/
} os_threadPriorities_t;

typedef struct {
    const char* name;                   /**< Name of the thread*/
    os_threadCallback_t threadCallback; /**< Code to execute in the thread*/
    void* threadArgs;                   /**< Arguments to pass to the thread callback*/
    uint32_t stackSize;                 /**< Amount of stack memory the thread may use*/
    os_threadPriorities_t priority;     /**< The thread priority for the scheduler*/
} os_threadConfig_t;

/**
 * @brief Create and deploy a new thread. The thread will not run until
 * os_startSchedular() is called.
 * @param   conf  Configuration structure for the thread.
 * @return  A handle to the currently created thread. If something went wrong,
 * NULL is returned.
 * @note    This function uses dynamic memory allocation
 */
os_threadHandle_t os_threadNew(os_threadConfig_t *conf);

/**
 * @brief Start the OS task scheduler. This will cause the created threads to run.
 * @return false If the scheduler could not be started because of insufficient
 * ram. If the device runs out of ram after it started, this function will return
 * as well.
 * @note This function should never return.
 */
void os_startScheduler(void);

/**
 * @brief Stop a running thread.
 * @param handle    Handle to the thread to stop.
 * @retval  true    If the thread is stopped successfully.
 * @retval  false   If thread could not be stopped.
 */
bool os_threadStop(os_threadHandle_t handle);

/**
 * @brief Delete a thread.
 * @details Stop and delete a thread from the memory.
 * DO NOT call this function before os_threadNew.
 * @param handle Handle to the thread that must be deleted.
 * @retval  true If the thread was deleted successfully.
 * @retval  false If the thread could not be deleted.
 */
bool os_threadDelete(os_threadHandle_t handle);


#ifdef  __cplusplus
}
#endif

#endif /* OS_THREAD_H */

/**
 *@}
 **/

```

For the full file, look in the files in the repository.