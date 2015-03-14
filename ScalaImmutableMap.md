Quick and dirty test with scala's default Map. We see the ultra-tight representation for small maps (<= 4 entries), but after that, the results are weird at some points. I recall that immutable.Map used to cache the last few updates before switching to a new table, but that was before the scala collections facelift - in the current version, it's just hard for me to see the implementation and grok it :-/

```
$scala -cp lib/guava-r8-trunk.jar:dist/object-explorer.jar 
Welcome to Scala version 2.8.0.final (OpenJDK 64-Bit Server VM, Java 1.6.0_18).
Type in expressions to have them evaluated.
Type :help for more information.

scala> import objectexplorer._    
import objectexplorer._

scala> import com.google.common.base.Predicates
import com.google.common.base.Predicates

scala> class X                                                       
defined class X

scala> val notX = Predicates.not(Predicates.instanceOf(classOf[X]))                                                                                             
notX: com.google.common.base.Predicate[java.lang.Object] = Not(IsInstanceOf(X))

scala> for (i <- 1 to 32) { 
     |   print(i + ": ")
     |   var map = Map[X, X]()
     |   for (j <- 1 to i) {
     |     map += new X -> new X
     | 
     |   }
     |   ObjectGraphMeasurer.measure(map, notX)
     |   println(ObjectGraphMeasurer.measure(map, notX))
     | }
1: Footprint{Objects=1, References=2, Primitives=[]}
2: Footprint{Objects=1, References=4, Primitives=[]}
3: Footprint{Objects=1, References=6, Primitives=[]}
4: Footprint{Objects=1, References=8, Primitives=[]}
5: Footprint{Objects=12, References=31, Primitives=[int x 7]}
6: Footprint{Objects=18, References=41, Primitives=[int x 12]}
7: Footprint{Objects=16, References=43, Primitives=[int x 9]}
8: Footprint{Objects=20, References=51, Primitives=[int x 12]}
9: Footprint{Objects=20, References=55, Primitives=[int x 11]}
10: Footprint{Objects=26, References=65, Primitives=[int x 16]}
11: Footprint{Objects=28, References=71, Primitives=[int x 17]}
12: Footprint{Objects=30, References=77, Primitives=[int x 18]}
13: Footprint{Objects=32, References=83, Primitives=[int x 19]}
14: Footprint{Objects=38, References=93, Primitives=[int x 24]}
15: Footprint{Objects=38, References=97, Primitives=[int x 23]}
16: Footprint{Objects=38, References=101, Primitives=[int x 22]}
17: Footprint{Objects=42, References=109, Primitives=[int x 25]}
18: Footprint{Objects=52, References=123, Primitives=[int x 34]}
19: Footprint{Objects=48, References=123, Primitives=[int x 29]}
20: Footprint{Objects=52, References=131, Primitives=[int x 32]}
21: Footprint{Objects=54, References=137, Primitives=[int x 33]}
22: Footprint{Objects=52, References=139, Primitives=[int x 30]}
23: Footprint{Objects=52, References=143, Primitives=[int x 29]}
24: Footprint{Objects=60, References=155, Primitives=[int x 36]}
25: Footprint{Objects=66, References=165, Primitives=[int x 41]}
26: Footprint{Objects=70, References=173, Primitives=[int x 44]}
27: Footprint{Objects=68, References=175, Primitives=[int x 41]}
28: Footprint{Objects=72, References=183, Primitives=[int x 44]}
29: Footprint{Objects=78, References=193, Primitives=[int x 49]}
30: Footprint{Objects=74, References=193, Primitives=[int x 44]}
31: Footprint{Objects=82, References=205, Primitives=[int x 51]}
32: Footprint{Objects=86, References=213, Primitives=[int x 54]}
```