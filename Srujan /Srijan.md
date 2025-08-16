```
puts "RM-Info: Running script [info script]\n"



set DESIGN_NAME                   "eight_bit_full_adder"  ;#  The name of the top-level design
#set DESIGN_NAME                   "eight_bit_full_adder"  ;#  The name of the top-level design
set PDK_PATH                        "./../ref"

#set DESIGN_REF_DATA_PATH          "$DESIGN_DIR"  ;#  Absolute path prefix variable for library/design data.
                                       #  Use this variable to prefix the common absolute path  
                                       #  to the common variables defined below.
                                       #  Absolute paths are mandatory for hierarchical 
                                       #  reference methodology flow.

##########################################################################################
# Hierarchical Flow Design Variables
##########################################################################################

set HIERARCHICAL_DESIGNS           "" ;# List of hierarchical block design names "DesignA DesignB" ...
set HIERARCHICAL_CELLS             "" ;# List of hierarchical block cell instance names "u_DesignA u_DesignB" ...

##########################################################################################
# Library Setup Variables
##########################################################################################

# For the following variables, use a blank space to separate multiple entries.
# Example: set TARGET_LIBRARY_FILES "lib1.db lib2.db lib3.db"

#  Additional search path to be added to the default search path
set ADDITIONAL_SEARCH_PATH        "$PDK_PATH $PDK_PATH/tech/milkyway $PDK_PATH/tech/star_rcxt"  

#Target technology logical libraries
set TARGET_LIBRARY_FILES          "$PDK_PATH/lib/stdcell_rvt/db_ccs/saed32rvt_tt0p78vn40c.db" 

#set TARGET_LIBRARY_FILES           "./../saed32rvt_ss0p7vn40c.db"  ;#  Target technology logical libraries

#set ADDITIONAL_LINK_LIB_FILES     ""  ;#  Extra link logical libraries not included in TARGET_LIBRARY_FILES

#set ADDITIONAL_LINK_LIB_FILES     "./../lc/full_adder.db"  ;#  Extra link logical libraries not included in TARGET_LIBRARY_FILES

set MIN_LIBRARY_FILES             ""  ;#  List of max min library pairs "max1 min1 max2 min2 max3 min3"...

set MW_REFERENCE_LIB_DIRS         ""  ;#  Milkyway reference libraries (include IC Compiler ILMs here)

set MW_REFERENCE_CONTROL_FILE     ""  ;#  Reference Control file to define the #Milkyway reference libs

#Milkyway technology file
set TECH_FILE                     "$PDK_PATH/tech/milkyway/saed32nm_1p9m_mw.tf"  ;

set MAP_FILE                      "saed32nm_tf_itf_tluplus.map"  ;#  Mapping file for TLUplus
set TLUPLUS_MAX_FILE              "saed32nm_1p9m_Cmax.tluplus"  ;#  Max TLUplus file
set TLUPLUS_MIN_FILE              "saed32nm_1p9m_Cmin.tluplus"  ;#  Min TLUplus file

set MIN_ROUTING_LAYER            "M1"   ;# Min routing layer
set MAX_ROUTING_LAYER            "M5"   ;# Max routing layer

set LIBRARY_DONT_USE_FILE        ""   ;# Tcl file with library modifications for dont_use
set LIBRARY_DONT_USE_PRE_COMPILE_LIST ""; #Tcl file for customized don't use list before first compile
set LIBRARY_DONT_USE_PRE_INCR_COMPILE_LIST "";# Tcl file with library modifications for dont_use before incr compile
##########################################################################################
# Multivoltage Common Variables
#
# Define the following multivoltage common variables for the reference methodology scripts 

```
