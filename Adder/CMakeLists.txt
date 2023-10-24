add_library(mearlymath adder.cpp adder.h) 

install(TARGETS mearlymath DESTINATION lib)
install(FILES adder.h DESTINATION include)