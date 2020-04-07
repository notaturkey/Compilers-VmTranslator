usage:
	For only one vm file:
		vm_translator.py .\path\to\vmFile.vm
		or
		vm_translator.py .\path\to\vmFile.vm -o .\pathTo\Output\
	
	Mutliple Vm Files:
		vm_translator.py .\path\to\vmFile\
		or
		vm_translator.py .\path\to\vmFile\ -o .\pathTo\Output\
	
	results in a single file called .asm


Strict compliance mode:
	vm_translator.py .\path\to\vmFile -o .\pathTo\Output -x 
	or
	vm_translator.py .\path\to\vmFile -o .\pathTo\Output --xHal
