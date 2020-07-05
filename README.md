# laminas-obfuscator-spec-rfc
Effective obfuscation has to deal with scrambling, packing, and encoding.
Many tools are available to obfuscate PHP files, however, it is hard to
find a suitable and open-source solution for obfuscating multiple modules or
source code files in a common PHP framework. This repository should be
treated as an RFC.  

We present this specification as a strong procedure to obfuscate and pack
Laminas applications. First, we define these processes:

- Variable scrambling: scramble class properties, constants, and local variables
in methods. At all times, the obfuscator should store the mapping for public
properties. Scope-limited variables mappings can be flushed after moving
to the next method.
- Method name scrambling: Scramble method names, and carefully replace
referenced method calls through inspecting type hints in a function definition,
if it's missing or an object of unknown class name is retrieved dynamically, 
pop errors and let the user write docblock for variables of unknown class names.
It is important to acknowledge the true class name of an instance, because only
by being aware of these class names we can replace the referenced method
name with the scrambled value, the same applies for scrambled public properties
and constants.  
What about magic functions? Do not scramble them, however, more
thoughts should be targeted for `__call`.  
A review or report should be produced at the end, indicating which variables
under a class -> method is assumed to be instances of certain classes.
This allows a manual review of the obfuscation results and decreases the chances
of errors due to sloppy treatment. 
- Class scrambling: scramble class names. Obfuscator must store the mapping
to a scrambled class name at all times. Storing the mapping is important,
and later on, the obfuscator must also replace the referenced class name
with the scrambled value. Additional care should exist for docblock
annotations and configuration where classes FQCN can be referenced through
PHP's `::class` notation or literal strings.
- Namespace scrambling: replace defined namespaces with a scrambled value.
Similar to class scrambling, we need to replace referenced namespaces in 
`use ...` statements and FQCN referencing locations (whether through
importing the class or not, and whether using `::class` notation or literally).
- Literal string encoding: It is nice to obfuscate in-line strings used in
method calls, do not use base64 -- use XOR or other encoding mechanisms by
providing in-code helper functions.
- Packing classes: In Laminas, classes are written under a specific module that
represents a real-world entity type. Get rid of this hierarchy by packing
all classes in a single PHP file under one namespace. Additionally, care should
be considered for changed autoload configuration in composer to reference
the packed file containing all classes. The resulting packed PHP file will
contain all classes under a single namespace, with identifiers (class names, 
properties, local variables..) scrambled.
- Packing app configuration: merge configuration files in ./config/ into one 
PHP file, also taking care of the scrambled namespaces/FQCNs.
- Obfuscating dependencies: If it's possible to obfuscate local modules, it
should be possible to obfuscate dependencies installed in the `vendor`
directory.

The obfuscator should be installed using composer as a development dependency,
and executables should be provided to obfuscate the current Laminas application.

RFCs are welcomed and important to create a robust specification, IMO scrambling
is more effective than encoding. It will drive the reverser crazy when
there exists only a single packed PHP file containing local and vendor modules, 
especially by additionally packing redundant code. Reverser will try to find 
logical similarities between obfuscated classes and open source code. Shuffling
the order of class order, constants, methods, and properties will be helpful.

What about generated files in data/, module's configuration, and views?



