**Search for all properties of a package given the name**
```
function (getListOfVarsStartingWith _prefix _varResult)
    get_cmake_property(_vars VARIABLES)
    string (REGEX MATCHALL "(^|;)${_prefix}[A-Za-z0-9_]*" _matchedVars "${_vars}")
    set (${_varResult} ${_matchedVars} PARENT_SCOPE)
endfunction()

getListOfVarsStartingWith("NAME_TO_SEARCH" matchedVars)
foreach (_var IN LISTS matchedVars)
	message(STATUS "NAME_TO_SEARCH var: ${_var}=${${_var}}")
endforeach()
```