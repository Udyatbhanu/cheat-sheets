# Kotlin Mastery Cheat Sheet

A single-file reference covering the language, the standard library, coroutines,
multiplatform, tooling, testing, idioms and design patterns. Targets Kotlin 2.x
on the JVM (version- and platform-specific features are flagged).

**Contents**

1. [Running Kotlin & Tooling](#1-running-kotlin--tooling)
2. [Basics: values, types, literals](#2-basics-values-types-literals)
3. [Null Safety](#3-null-safety)
4. [Strings](#4-strings)
5. [Control Flow](#5-control-flow)
6. [Functions](#6-functions)
7. [Lambdas & Higher-Order Functions](#7-lambdas--higher-order-functions)
8. [Classes & Objects](#8-classes--objects)
9. [Data, Value, Sealed & Enum Classes](#9-data-value-sealed--enum-classes)
10. [Interfaces, Inheritance & Delegation](#10-interfaces-inheritance--delegation)
11. [Properties & Delegated Properties](#11-properties--delegated-properties)
12. [Objects, Companions & Singletons](#12-objects-companions--singletons)
13. [Generics & Variance](#13-generics--variance)
14. [Extensions](#14-extensions)
15. [Scope Functions](#15-scope-functions)
16. [Collections & Sequences](#16-collections--sequences)
17. [Operators & Operator Overloading](#17-operators--operator-overloading)
18. [Exceptions & Result](#18-exceptions--result)
19. [Coroutines & Flow](#19-coroutines--flow)
20. [Annotations, Reflection & Metaprogramming](#20-annotations-reflection--metaprogramming)
21. [DSLs & Type-Safe Builders](#21-dsls--type-safe-builders)
22. [Java Interop](#22-java-interop)
23. [Serialization, Time, IO](#23-serialization-time-io)
24. [Testing](#24-testing)
25. [Gradle, Multiplatform & Ecosystem](#25-gradle-multiplatform--ecosystem)
26. [Design Patterns in Kotlin](#26-design-patterns-in-kotlin)
27. [Idioms, Gotchas & Best Practices](#27-idioms-gotchas--best-practices)
28. [One-Liners & Cookbook](#28-one-liners--cookbook)

---

## 1. Running Kotlin & Tooling

```bash
kotlinc hello.kt -include-runtime -d hello.jar && java -jar hello.jar
kotlinc-jvm                       # REPL
kotlin script.main.kts            # run a .main.kts script
kotlinc -script script.kts
gradle init --type kotlin-application
./gradlew run test build check ktlintCheck detekt
./gradlew dependencies --configuration runtimeClasspath
kotlinc -Xjavac-arguments=... -jvm-target 21 -Werror -progressive
```

```kotlin
fun main() = println("hi")                 // entry point
fun main(args: Array<String>) { }          // with argv
```

Tooling: Gradle Kotlin DSL (`build.gradle.kts`), `ktlint`/`ktfmt` (format),
`detekt` (static analysis), `kover`/`jacoco` (coverage), `kotlinx-*` libs
(coroutines, serialization, datetime, atomicfu), KSP (annotation processing),
`kotlin-scripting`, Compose Multiplatform, Ktor, Exposed, Arrow.

## 2. Basics: values, types, literals

```kotlin
val immutable = 1              // read-only (prefer)
var mutable: Int = 2           // reassignable
const val COMPILE_TIME = "x"   // top-level/object only, primitives & String
lateinit var svc: Service      // non-null, initialized later (no primitives)
```

Types: `Byte Short Int Long Float Double Char Boolean String Any Unit Nothing`,
`UByte UShort UInt ULong`, arrays `IntArray`, `Array<T>`.

```kotlin
val i = 42; val l = 42L; val d = 1.0; val f = 1.0f
val hex = 0xFF; val bin = 0b1010; val big = 1_000_000
val u = 42u; val c = 'a'; val b = true
val n: Number = i
i.toLong(); l.toInt(); "42".toInt(); "x".toIntOrNull()   // explicit conversions
Int.MAX_VALUE; Long.MIN_VALUE; Double.NaN; Double.POSITIVE_INFINITY
7 / 2            // 3 (integer division)
7.0 / 2          // 3.5
7 % 3; (-7).mod(3); 7 shl 1; 7 shr 1; 7 ushr 1; 7 and 3; 7 or 3; 7 xor 3; i.inv()
```

`Unit` = "no meaningful value" (like `void`); `Nothing` = never returns
(`throw`, infinite loop) and is a subtype of everything.

```kotlin
val x: Any = "s"
if (x is String) x.length              // smart cast
val s = x as String                    // unsafe cast (throws)
val s2 = x as? String                  // safe cast -> String?
```

## 3. Null Safety

```kotlin
var a: String = "x"          // cannot be null
var b: String? = null        // nullable
b?.length                    // safe call -> Int?
b?.let { println(it) }       // run only if non-null
b?.length ?: 0               // Elvis: default
b ?: return                  // Elvis + early return/throw
b ?: throw IllegalStateException("missing")
b!!.length                   // NPE if null — avoid
val len = b?.length ?: -1
list?.get(0)?.name?.trim()   // safe chaining
requireNotNull(b) { "b required" }; checkNotNull(b)
b.orEmpty()                  // String?/List? -> non-null
listOfNotNull(a, b)
nullableList?.filterNotNull()
```

Platform types from Java are `String!` — annotate Java with `@Nullable`/`@NotNull`
or wrap at the boundary. Prefer `lateinit`, `by lazy`, or a nullable + Elvis over `!!`.

## 4. Strings

```kotlin
val name = "World"
"Hello, $name! ${name.length} chars"          // templates
"""
  Raw string, no escapes: C:\path\to
  ${'$'} literal dollar
""".trimIndent()                              // or .trimMargin("|")
"a" + 1; "ab".plus("c")
s.length; s[0]; s.first(); s.last(); s.getOrNull(9)
s.uppercase(); s.lowercase(); s.replaceFirstChar { it.titlecase() }
s.trim(); s.trimStart(); s.trimEnd(); s.padStart(5, '0'); s.padEnd(5)
s.isEmpty(); s.isBlank(); s.isNotBlank(); s.orEmpty(); s.ifBlank { "def" }
s.split(",", limit = 2); s.split(Regex("\\s+")); s.lines(); s.chunked(3)
list.joinToString(separator = ", ", prefix = "[", postfix = "]") { it.name }
s.substring(1, 3); s.substringBefore(":"); s.substringAfterLast("/")
s.take(3); s.drop(3); s.takeLast(2); s.dropLastWhile { it == ' ' }
s.startsWith("He", ignoreCase = true); s.endsWith("d"); s.contains("or")
s.indexOf("o"); s.lastIndexOf("o"); s.count { it == 'l' }
s.replace("l", "L"); s.replace(Regex("\\d+"), "#"); s.removePrefix("He")
s.reversed(); s.repeat(3); s.toCharArray(); s.toByteArray(Charsets.UTF_8)
s.equals(other, ignoreCase = true); s.compareTo(other)
buildString { append("a"); appendLine("b") }
String.format("%,.2f", 1234.5); "%s=%d".format("a", 1)
```

Regex:

```kotlin
val re = Regex("""(?<num>\d+)-(\w+)""", RegexOption.IGNORE_CASE)
re.matches(s); re.containsMatchIn(s)
val m = re.find(s)
m?.value; m?.groupValues?.get(1); m?.groups?.get("num")?.value; m?.range
re.findAll(s).map { it.value }.toList()
re.replace(s, "X"); re.replace(s) { "[${it.value}]" }
re.split(s); Regex.escape("a.b")
val (num, word) = re.find(s)!!.destructured
```

## 5. Control Flow

```kotlin
if (a > b) max = a else max = b
val max = if (a > b) a else b                 // if is an expression

val desc = when (x) {                          // when as expression (exhaustive)
    0, 1 -> "small"
    in 2..9 -> "medium"
    !in 10..20 -> "out of range"
    is String -> "string of ${x.length}"
    else -> "other"
}
when {                                         // no subject: replaces if-else chains
    x > 10 -> "big"
    else -> "small"
}
when (val r = compute()) { is Ok -> r.value; is Err -> throw r.e }

for (i in 1..10) {}                 // inclusive
for (i in 1 until 10) {}            // exclusive
for (i in 10 downTo 1 step 2) {}
for (i in 0..<n) {}                 // 2.0 range-until operator
for (c in "abc") {}
for ((i, v) in list.withIndex()) {}
for ((k, v) in map) {}
list.indices; list.lastIndex

while (cond) {}
do {} while (cond)

outer@ for (i in 1..3) {
    for (j in 1..3) {
        if (j == 2) continue@outer
        if (i == 3) break@outer
    }
}
run loop@{ list.forEach { if (it == 0) return@loop } }    // labeled non-local return
```

## 6. Functions

```kotlin
fun add(a: Int, b: Int = 0): Int { return a + b }
fun square(x: Int) = x * x                      // expression body, inferred type
fun log(msg: String, vararg tags: String) {}     // varargs; call log("m", *arr)
fun greet(name: String, greeting: String = "Hi") = "$greeting, $name"
greet(greeting = "Yo", name = "Bo")              // named arguments
fun unit(): Unit {}                              // returns Unit
fun fail(msg: String): Nothing = throw IllegalStateException(msg)

infix fun Int.pow(e: Int): Int = ...             // 2 pow 8
tailrec fun sum(n: Int, acc: Int = 0): Int = if (n == 0) acc else sum(n - 1, acc + n)
inline fun <reified T> parse(s: String): T = ... // reified type at runtime
operator fun Point.plus(o: Point) = ...
suspend fun fetch(): String = ...                // coroutine
fun local() {
    fun helper() = 1                             // local function
    helper()
}
context(Logger) fun work() { }                   // context receivers (experimental)
```

Single expression + trailing lambda + named args replace most builder overloads.

## 7. Lambdas & Higher-Order Functions

```kotlin
val sum: (Int, Int) -> Int = { a, b -> a + b }
val inc = { x: Int -> x + 1 }
val printIt: (String) -> Unit = ::println          // function reference
val len = String::length                           // bound/unbound refs
val ctor = ::Point
list.map { it.name }                               // implicit `it`
list.forEach { item -> }
fun runTwice(block: () -> Unit) { block(); block() }
runTwice { println("x") }                          // trailing lambda outside parens
fun <T, R> transform(x: T, f: (T) -> R): R = f(x)
val withReceiver: StringBuilder.() -> Unit = { append("x") }   // receiver lambda
list.fold(0) { acc, x -> acc + x }
val curried: (Int) -> (Int) -> Int = { a -> { b -> a + b } }
val f = { a: Int, _: Int -> a }                    // unused param
crossinline / noinline                             // control lambda inlining
```

```kotlin
inline fun measure(block: () -> Unit): Long {      // inline avoids lambda alloc,
    val t = System.nanoTime(); block()             // enables non-local return
    return System.nanoTime() - t
}
```

## 8. Classes & Objects

```kotlin
class Person(val name: String, var age: Int = 0) {      // primary constructor
    var nickname: String? = null
        private set                                     // custom visibility

    init { require(age >= 0) { "age >= 0" } }           // init block

    constructor(name: String) : this(name, 0)           // secondary constructor

    fun greet() = "Hi $name"
    override fun toString() = "Person($name)"

    inner class Badge { val owner = this@Person }       // inner: holds outer ref
    class Address                                       // nested: no outer ref
    companion object { const val MAX = 100 }
}

val p = Person("Ann")                    // no `new`
p.age = 3; p.name                        // property access syntax
```

Visibility: `public` (default), `internal` (module), `protected`, `private`.
Classes are `final` by default — mark `open` to subclass; `abstract`; `sealed`.

## 9. Data, Value, Sealed & Enum Classes

```kotlin
data class User(val id: Int, val name: String = "anon", val tags: List<String> = emptyList())
// generates equals/hashCode/toString/copy/componentN (from primary ctor vals only)
val u2 = u.copy(name = "new")
val (id, name) = u2                       // destructuring
```

```kotlin
@JvmInline
value class Email(val raw: String) {      // inline/value class: zero-overhead wrapper
    init { require("@" in raw) }
}
```

```kotlin
sealed interface Result<out T>            // exhaustive hierarchy, known at compile time
data class Ok<T>(val value: T) : Result<T>
data class Err(val error: Throwable) : Result<Nothing>
data object Loading : Result<Nothing>     // 1.9+ data object (nice toString)

fun <T> handle(r: Result<T>) = when (r) { // no `else` needed — exhaustive
    is Ok -> r.value
    is Err -> throw r.error
    Loading -> null
}

sealed class Shape { abstract val area: Double }   // sealed class: state + hierarchy
```

```kotlin
enum class Color(val hex: String) {
    RED("#f00"), GREEN("#0f0") { override fun pretty() = "green!" };
    open fun pretty() = name.lowercase()
}
Color.RED.name; Color.RED.ordinal; Color.valueOf("RED"); Color.entries   // 1.9+
enumValues<Color>(); enumValueOf<Color>("RED")
```

## 10. Interfaces, Inheritance & Delegation

```kotlin
interface Clickable {
    val label: String                    // abstract property (no backing field)
    fun click()                           // abstract
    fun showOff() = println("clickable")  // default implementation
}

abstract class Base(val id: Int) {
    open fun render() {}
    abstract fun validate()
}

class Button(id: Int, override val label: String) : Base(id), Clickable {
    override fun click() {}
    override fun validate() {}
    final override fun render() { super.render() }
}

interface A { fun f() = "A" }
interface B { fun f() = "B" }
class C : A, B { override fun f() = super<A>.f() + super<B>.f() }   // disambiguate
```

Class delegation (composition without boilerplate):

```kotlin
class LoggingList<T>(private val inner: MutableList<T> = mutableListOf()) :
    MutableList<T> by inner {
    override fun add(element: T): Boolean {
        println("add $element"); return inner.add(element)
    }
}
```

## 11. Properties & Delegated Properties

```kotlin
class Temp {
    var celsius: Double = 0.0
        get() = field                       // backing field
        set(value) {
            require(value > -273.15)
            field = value
        }
    val fahrenheit: Double get() = celsius * 9 / 5 + 32     // computed, no field
    @JvmField val exposed = 1                                // expose as Java field
}
```

```kotlin
val heavy: Config by lazy { loadConfig() }                 // thread-safe by default
val cheap by lazy(LazyThreadSafetyMode.NONE) { 1 }
var name: String by Delegates.observable("") { _, old, new -> log(old, new) }
var age: Int by Delegates.vetoable(0) { _, _, new -> new >= 0 }
var late: String by Delegates.notNull()
val cfg: String by map                                     // map-backed properties
class Owner { var v: Int by Counter() }                     // custom delegate

class Counter : ReadWriteProperty<Any?, Int> {
    private var n = 0
    override fun getValue(thisRef: Any?, property: KProperty<*>) = n
    override fun setValue(thisRef: Any?, property: KProperty<*>, value: Int) { n = value }
}
operator fun provideDelegate(thisRef: Any?, prop: KProperty<*>): ReadOnlyProperty<...>
```

## 12. Objects, Companions & Singletons

```kotlin
object Registry {                        // singleton, thread-safe lazy init
    private val items = mutableMapOf<String, Any>()
    fun put(k: String, v: Any) { items[k] = v }
}
Registry.put("a", 1)

class Client private constructor(val url: String) {
    companion object Factory {           // one per class; statics live here
        const val DEFAULT = "https://x"
        @JvmStatic fun create(url: String = DEFAULT) = Client(url)
        operator fun invoke(url: String) = create(url)     // Client("u") works
    }
}

val listener = object : Listener {        // anonymous object (object expression)
    override fun onEvent(e: Event) {}
}
val adhoc = object { val x = 1 }          // ad-hoc type (local scope only)
```

## 13. Generics & Variance

```kotlin
class Box<T>(val item: T)
fun <T> singletonList(x: T): List<T> = listOf(x)
fun <T : Comparable<T>> max(a: T, b: T) = if (a > b) a else b       // upper bound
fun <T> where2(x: T) where T : CharSequence, T : Comparable<T> = x  // multiple bounds
class Producer<out T>(private val v: T) { fun get(): T = v }        // covariant
class Consumer<in T> { fun accept(v: T) {} }                        // contravariant
fun copy(from: List<out Any>, to: MutableList<in Any>) {}           // use-site variance
fun printAll(list: List<*>) {}                                      // star projection
inline fun <reified T> Gson.fromJson(s: String): T = fromJson(s, T::class.java)
fun <T : Any> requireT(x: T?): T = x!!             // T : Any = non-nullable
@JvmName("sumInts") fun List<Int>.sum2(): Int = 0  // avoid erasure clashes
```

Rule of thumb (PECS): producers are `out`, consumers are `in`. Type parameters are
erased at runtime unless `reified` in an `inline` function.

## 14. Extensions

```kotlin
fun String.shout() = uppercase() + "!"                    // extension function
val String.initials: String get() = split(" ").map { it.first() }.joinToString("")
fun <T> List<T>.second(): T = this[1]
fun Int?.orZero() = this ?: 0                             // nullable receiver
fun MutableList<Int>.swap(i: Int, j: Int) { val t = this[i]; this[i] = this[j]; this[j] = t }
operator fun Point.plus(o: Point) = Point(x + o.x, y + o.y)
fun StringBuilder.appendTwice(s: String) = append(s).append(s)
class Scope { fun String.local() = length }                // member extension
```

Extensions are resolved **statically** (no override/dispatch), cannot access
`private` members, and a member always wins over an extension with the same signature.

## 15. Scope Functions

| Function | Receiver | Returns | Typical use |
|---|---|---|---|
| `let` | `it` | lambda result | null-safe transform, scoping a value |
| `run` | `this` | lambda result | compute a value in a receiver scope |
| `run {}` (no recv) | – | lambda result | run a block as an expression |
| `with(x) {}` | `this` | lambda result | grouped calls on an object |
| `apply` | `this` | receiver | configure/initialize an object |
| `also` | `it` | receiver | side effects (logging, validation) |

```kotlin
val len = str?.let { it.length } ?: 0
val cfg = Config().apply { host = "x"; port = 8080 }
val out = with(StringBuilder()) { append("a"); append("b"); toString() }
val user = fetch().also { log.info("got {}", it) }
val text = run { if (flag) "a" else "b" }
value.takeIf { it > 0 }?.let(::use)
value.takeUnless { it.isBlank() }
repeat(3) { i -> println(i) }
requireNotNull(x); require(cond) { "msg" }; check(state); error("boom"); TODO("later")
```

## 16. Collections & Sequences

```kotlin
listOf(1, 2); mutableListOf(); emptyList(); listOfNotNull(a, b); buildList { add(1) }
setOf(); mutableSetOf(); linkedSetOf(); sortedSetOf()
mapOf("a" to 1); mutableMapOf(); linkedMapOf(); buildMap { put("a", 1) }
arrayOf(1, 2); intArrayOf(1); Array(3) { it * it }; IntArray(3)
List(5) { it }; MutableList(3) { 0 }
(1..10).toList(); generateSequence(1) { it * 2 }.take(5).toList()
```

Read-only interfaces (`List`, `Map`, `Set`) vs mutable (`MutableList`, ...) —
read-only is a *view contract*, not deep immutability (use `kotlinx.collections.immutable`
for persistent collections).

```kotlin
// access
list[0]; list.first(); list.firstOrNull(); list.last(); list.getOrNull(9)
list.getOrElse(9) { -1 }; list.elementAtOrNull(3); list.single(); list.random()
map["k"]; map.getOrDefault("k", 0); map.getOrElse("k") { 0 }; map.getValue("k")
map.getOrPut("k") { compute() }                              // MutableMap

// transform
list.map { it * 2 }; list.mapIndexed { i, v -> i to v }; list.mapNotNull { f(it) }
list.flatMap { it.children }; list.flatten(); list.zip(other); list.unzip()
list.associate { it.id to it }; list.associateBy { it.id }; list.associateWith { f(it) }
map.mapKeys { it.key.trim() }; map.mapValues { (_, v) -> v * 2 }
list.withIndex(); list.chunked(3); list.windowed(3, step = 1, partialWindows = false)
list.zipWithNext { a, b -> b - a }; list.runningFold(0) { a, b -> a + b }

// filter / test
list.filter { it > 0 }; list.filterNot {}; list.filterNotNull(); list.filterIsInstance<String>()
list.filterIndexed { i, _ -> i % 2 == 0 }; map.filterKeys {}; map.filterValues {}
list.partition { it > 0 }                     // Pair(matching, rest)
list.any {}; list.all {}; list.none {}; list.count {}; list.contains(x); x in list
list.find {}; list.findLast {}; list.indexOfFirst {}; list.isEmpty(); list.isNotEmpty()

// order
list.sorted(); list.sortedDescending(); list.sortedBy { it.age }
list.sortedWith(compareBy<User> { it.age }.thenByDescending { it.name })
list.reversed(); list.asReversed(); list.shuffled(); mutableList.sortBy { it.age }
compareBy<T>(nullsLast()); list.maxByOrNull { it.age }; list.minWithOrNull(cmp)

// aggregate
list.sum(); list.sumOf { it.price }; list.average(); list.maxOrNull(); list.minOrNull()
list.reduce { a, b -> a + b }; list.fold(0) { a, b -> a + b }; list.foldRight(...)
list.groupBy { it.dept }; list.groupingBy { it.dept }.eachCount()
list.joinToString(); list.distinct(); list.distinctBy { it.id }
list.take(3); list.takeWhile {}; list.drop(2); list.dropLastWhile {}
list + other; list - other; list.union(o); list.intersect(o); list.subtract(o)
list.toSet(); list.toMutableList(); list.toTypedArray(); map.toList()

// mutate
ml.add(x); ml.addAll(o); ml.remove(x); ml.removeAt(0); ml.removeAll { it < 0 }
ml.retainAll {}; ml[0] = x; ml.clear(); ml += x; ml -= x; ml.shuffle(); ml.sort()
mm["k"] = v; mm.put(k, v); mm.putIfAbsent(k, v); mm.remove(k); mm += ("k" to v)
mm.merge(k, 1) { a, b -> a + b }; mm.compute(k) { _, v -> (v ?: 0) + 1 }
```

Sequences (lazy, single-pass — avoids intermediate lists on big pipelines):

```kotlin
list.asSequence()
    .filter { it.active }
    .map { it.name }
    .take(10)
    .toList()                                  // terminal operation triggers work
generateSequence { readLine() }.takeWhile { it != null }
sequence { yield(1); yieldAll(2..4) }          // suspending sequence builder
file.useLines { lines -> lines.count() }       // streams a file lazily
```

## 17. Operators & Operator Overloading

```kotlin
operator fun plus/minus/times/div/rem/unaryMinus/unaryPlus/not/inc/dec
operator fun plusAssign/minusAssign(...)          // += for mutable types
operator fun get(i: Int) / set(i: Int, v: T)      // a[i], a[i] = v
operator fun invoke(...)                          // a(...)
operator fun contains(x: T): Boolean              // x in a
operator fun compareTo(o: T): Int                 // <, <=, >, >=
operator fun equals(other: Any?): Boolean         // == (null-safe)
operator fun rangeTo(o: T) / rangeUntil(o: T)     // a..b, a..<b
operator fun iterator(): Iterator<T>              // for (x in a)
operator fun component1(), component2()           // destructuring
operator fun getValue/setValue(...)               // property delegation
infix fun ... ; a === b                           // referential identity
```

## 18. Exceptions & Result

```kotlin
try {
    risky()
} catch (e: IllegalArgumentException) {
    log.warn("bad input", e)
} catch (e: Exception) {
    throw AppException("wrapped", e)
} finally {
    cleanup()
}
val v = try { s.toInt() } catch (e: NumberFormatException) { 0 }   // try is an expression
```

Kotlin has **no checked exceptions** (`@Throws` to declare them for Java callers).

```kotlin
class AppException(msg: String, cause: Throwable? = null) : RuntimeException(msg, cause)

runCatching { risky() }
    .onSuccess { use(it) }
    .onFailure { log.error("failed", it) }
    .getOrElse { fallback }
    // also: getOrNull(), getOrThrow(), map/mapCatching/recover/fold
```

For domain errors prefer a sealed `Result`/`Either` type over exceptions
(`kotlin.Result` doesn't support cancellation-safe coroutine use — never
`runCatching` around cancellable code without rethrowing `CancellationException`).

```kotlin
resource.use { r -> r.read() }        // AutoCloseable, closes on exit (try-with-resources)
```

## 19. Coroutines & Flow

```kotlin
// build.gradle.kts: implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:…")
suspend fun load(): Data = withContext(Dispatchers.IO) { db.query() }

fun main() = runBlocking {                     // bridge blocking <-> suspending (tests/main)
    val job = launch { work() }                // fire & forget -> Job
    val deferred = async { compute() }          // returns value -> Deferred<T>
    val result = deferred.await()
    job.join(); job.cancel(); job.cancelAndJoin()
}

coroutineScope {                                // suspends until all children finish;
    launch { a() }                              // failure cancels siblings
    launch { b() }
}
supervisorScope { }                             // child failure doesn't cancel siblings
withTimeout(1_000) { slow() }                   // throws TimeoutCancellationException
withTimeoutOrNull(1_000) { slow() }             // returns null
delay(100)                                      // non-blocking sleep
yield(); currentCoroutineContext(); coroutineContext[Job]
```

Dispatchers & context: `Dispatchers.Default` (CPU), `IO` (blocking IO),
`Main` (UI, Android/Compose), `Unconfined`, `limitedParallelism(n)`,
`newSingleThreadContext`. Context elements combine: `Dispatchers.IO + Job() +
CoroutineName("x") + CoroutineExceptionHandler { _, e -> }`.

Structured concurrency: every coroutine belongs to a scope; scopes propagate
cancellation and wait for children. Own a `CoroutineScope` per lifecycle
(`viewModelScope`, `lifecycleScope`, or `CoroutineScope(SupervisorJob() + Dispatchers.IO)`
that you cancel). Never use `GlobalScope`.

```kotlin
// cancellation is cooperative
while (isActive) { compute() }                  // check isActive in tight loops
ensureActive()
try { work() } catch (e: CancellationException) { throw e }   // never swallow it
withContext(NonCancellable) { criticalCleanup() }
suspendCancellableCoroutine { cont ->           // wrap callback APIs
    api.call(object : Cb { override fun ok(v: T) = cont.resume(v) })
    cont.invokeOnCancellation { api.cancel() }
}
```

Flow (cold async streams):

```kotlin
fun ticker(): Flow<Int> = flow {
    var i = 0
    while (true) { emit(i++); delay(1000) }
}
flowOf(1, 2, 3); listOf(1, 2).asFlow(); channelFlow { send(1) }; callbackFlow { … }

ticker()
    .map { it * 2 }
    .filter { it % 3 != 0 }
    .onEach { log(it) }
    .flatMapLatest { fetch(it) }       // also flatMapConcat / flatMapMerge
    .distinctUntilChanged()
    .debounce(300).sample(1000)
    .buffer().conflate()
    .combine(other) { a, b -> a to b } // also zip, merge
    .retryWhen { cause, attempt -> attempt < 3 }
    .catch { emit(fallback) }
    .onStart { }.onCompletion { }
    .flowOn(Dispatchers.IO)            // upstream dispatcher
    .stateIn(scope, SharingStarted.WhileSubscribed(), initial)   // hot StateFlow
    .collect { render(it) }            // terminal: collect/first/toList/fold/launchIn(scope)
```

Hot streams & primitives: `MutableStateFlow(v)` (conflated, always has a value),
`MutableSharedFlow(replay = 1)`, `Channel(capacity)` with `send`/`receive`/
`consumeAsFlow`, `produce {}`, `actor {}`, `Mutex().withLock {}`, `Semaphore(n)`,
`AtomicInteger`/`atomicfu`.

Testing: `kotlinx-coroutines-test` — `runTest { }`, `advanceUntilIdle()`,
`advanceTimeBy(ms)`, `StandardTestDispatcher`/`UnconfinedTestDispatcher`,
`Dispatchers.setMain(...)`, `Turbine` for flows.

## 20. Annotations, Reflection & Metaprogramming

```kotlin
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION)
@Retention(AnnotationRetention.RUNTIME)
annotation class Route(val path: String)

@Route("/users") class UsersController
@Deprecated("Use v2", ReplaceWith("v2()"), DeprecationLevel.WARNING)
@Suppress("UNCHECKED_CAST") @OptIn(ExperimentalStdlibApi::class)
@RequiresOptIn @JvmName @JvmStatic @JvmOverloads @JvmField @Throws @Volatile @Synchronized
@get:JvmName("x") @field:Inject @Serializable @Transient   // use-site targets
```

```kotlin
// kotlin-reflect on the classpath
val cls = obj::class            // KClass
cls.simpleName; cls.qualifiedName; cls.isData; cls.sealedSubclasses; cls.objectInstance
cls.memberProperties.forEach { println("${it.name}=${it.get(obj)}") }
cls.declaredFunctions.first { it.name == "run" }.call(obj)
cls.primaryConstructor?.callBy(mapOf())
cls.findAnnotation<Route>()?.path
String::class.java              // KClass -> Class
typeOf<List<String>>()          // KType, works with reified generics
```

Prefer KSP/compiler plugins (`kotlinx.serialization`, Room, Dagger/Hilt, Compose)
over runtime reflection — faster and multiplatform-friendly.

## 21. DSLs & Type-Safe Builders

```kotlin
@DslMarker annotation class HtmlDsl              // prevents implicit outer receivers

@HtmlDsl class TagBuilder(val name: String) {
    private val children = mutableListOf<TagBuilder>()
    private val attrs = mutableMapOf<String, String>()
    fun attr(k: String, v: String) { attrs[k] = v }
    fun tag(name: String, block: TagBuilder.() -> Unit) =
        TagBuilder(name).apply(block).also { children += it }
    override fun toString(): String =
        "<$name${attrs.entries.joinToString("") { " ${it.key}=\"${it.value}\"" }}>" +
        children.joinToString("") + "</$name>"
}

fun html(block: TagBuilder.() -> Unit) = TagBuilder("html").apply(block)

val page = html {
    tag("body") {
        tag("h1") { attr("class", "title") }
    }
}
```

Ingredients: lambdas with receiver (`T.() -> Unit`), `apply`/`buildList`-style
builders, extension functions, `infix` functions, operator overloading,
default+named args, `@DslMarker`. Real examples: Gradle Kotlin DSL, Ktor routing,
kotlinx.html, Compose, Exposed SQL.

## 22. Java Interop

```kotlin
// Kotlin calling Java
val list = ArrayList<String>()             // Java types are usable directly
val len = javaObj.getName().length          // getters/setters as properties: javaObj.name
javaObj.field = 1
val cls = MyClass::class.java
SAM conversion: executor.execute { work() } // Java interface with one method -> lambda
try { javaThrows() } catch (e: IOException) {}   // checked exceptions are unchecked here
val nn: String = javaMaybeNull() ?: "default"    // platform type: guard nullability
```

```kotlin
// Making Kotlin pleasant from Java
@JvmStatic fun create()                 // static method (in companion/object)
@JvmField val x = 1                     // public field, not getter
@JvmOverloads fun f(a: Int, b: Int = 0) // generates overloads for defaults
@JvmName("processStrings") fun process(x: List<String>) {}
@file:JvmName("Utils")                  // file class name (top-level decls)
@Throws(IOException::class) fun read()  // declares checked exception
```

Gotchas: Kotlin `Int` maps to `int`/`Integer` (boxing at generic boundaries);
`Array<String>` ≠ `Array<out String>` variance; `internal` becomes public in
bytecode with name mangling; `object` singletons are accessed via `INSTANCE`.

## 23. Serialization, Time, IO

```kotlin
// kotlinx.serialization (plugin: kotlin("plugin.serialization"))
@Serializable
data class User(
    val id: Int,
    @SerialName("full_name") val name: String,
    @Transient val secret: String = "",
)
val json = Json { ignoreUnknownKeys = true; prettyPrint = true; encodeDefaults = true }
val s = json.encodeToString(user)
val u = json.decodeFromString<User>(s)
Json.parseToJsonElement(s).jsonObject["id"]?.jsonPrimitive?.int
// polymorphism: @Serializable sealed classes work out of the box
```

```kotlin
// kotlinx-datetime (multiplatform) / java.time (JVM)
val now = Clock.System.now()                       // Instant
now.toLocalDateTime(TimeZone.of("Asia/Kolkata"))
LocalDate(2024, 1, 31).plus(1, DateTimeUnit.MONTH)
Instant.parse("2024-01-01T00:00:00Z"); 5.seconds; 1.5.hours   // kotlin.time Duration
measureTime { work() }; measureTimedValue { compute() }
val mark = TimeSource.Monotonic.markNow(); mark.elapsedNow()
```

```kotlin
import java.io.File
import kotlin.io.path.*
File("a.txt").readText(); writeText(s); appendText(s); readLines()
File("a.txt").useLines { it.filter { l -> "ERROR" in l }.count() }
File("dir").walkTopDown().filter { it.extension == "kt" }.toList()
File("a").copyTo(File("b"), overwrite = true); file.deleteRecursively(); file.mkdirs()
Path("a/b.txt").createParentDirectories().writeText("x")   // kotlin.io.path
Path("dir").listDirectoryEntries("*.kt")
readln(); readlnOrNull(); println(x); print(x)
System.getenv("HOME"); System.getProperty("user.dir"); exitProcess(1)
```

## 24. Testing

```kotlin
// kotlin-test + JUnit5
import kotlin.test.*

class CalcTest {
    private lateinit var calc: Calc

    @BeforeTest fun setup() { calc = Calc() }
    @AfterTest fun teardown() {}

    @Test fun `adds two numbers`() {                  // backtick names
        assertEquals(3, calc.add(1, 2))
        assertNotNull(calc.result)
        assertTrue(calc.ok, "should be ok")
        assertContentEquals(listOf(1, 2), calc.items)
        assertFailsWith<IllegalArgumentException> { calc.add(-1, 0) }
        assertIs<Ok>(result)
    }

    @Ignore @Test fun pending() {}
}
```

```kotlin
// JUnit5 extras
@ParameterizedTest @ValueSource(ints = [1, 2, 3]) fun t(n: Int) {}
@Nested inner class WhenEmpty { @Test fun x() {} }
@TestInstance(TestInstance.Lifecycle.PER_CLASS)

// MockK
val repo = mockk<Repo>()
every { repo.get(1) } returns User(1, "a")
coEvery { repo.load() } returns data                  // suspend functions
verify(exactly = 1) { repo.get(1) }; coVerify { repo.load() }
val spy = spyk(RealRepo()); val slot = slot<User>(); every { repo.save(capture(slot)) } just Runs
mockkStatic(::now); mockkObject(Registry)

// Coroutines
@Test fun flows() = runTest {
    val vm = ViewModel(StandardTestDispatcher(testScheduler))
    vm.load(); advanceUntilIdle()
    vm.state.test { assertEquals(Loaded, awaitItem()); cancelAndIgnoreRemainingEvents() }  // Turbine
}
```

Also: Kotest (`shouldBe`, `StringSpec`, property testing), AssertJ/Truth,
Testcontainers, `@get:Rule`, `kover` coverage, `./gradlew test --tests "*CalcTest*"`.

## 25. Gradle, Multiplatform & Ecosystem

```kotlin
// build.gradle.kts (JVM app)
plugins {
    kotlin("jvm") version "2.0.20"
    kotlin("plugin.serialization") version "2.0.20"
    application
}
repositories { mavenCentral() }
dependencies {
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.9.0")
    api("com.example:lib:1.0")                 // leaks to consumers; prefer implementation
    testImplementation(kotlin("test"))
    testImplementation("io.mockk:mockk:1.13.12")
}
kotlin {
    jvmToolchain(21)
    compilerOptions { freeCompilerArgs.addAll("-Xjsr305=strict"); allWarningsAsErrors.set(true) }
}
application { mainClass.set("com.example.MainKt") }
tasks.test { useJUnitPlatform() }
```

```kotlin
// Kotlin Multiplatform skeleton
kotlin {
    jvm(); iosArm64(); js(IR) { browser() }; linuxX64(); wasmJs()
    sourceSets {
        commonMain.dependencies { implementation(libs.coroutines.core) }
        commonTest.dependencies { implementation(kotlin("test")) }
    }
}
// expect/actual for platform-specific code:
expect fun platformName(): String              // commonMain
actual fun platformName() = "JVM"              // jvmMain
```

Ecosystem worth knowing: Ktor (server/client), Spring Boot + Kotlin,
Exposed/JOOQ/Room (DB), Compose Multiplatform & Jetpack Compose (UI),
Arrow (functional), kotlinx-serialization/datetime/atomicfu, Koin/Hilt (DI),
Detekt/Ktlint, Dokka (docs), Kotlin scripting (`.main.kts`).

## 26. Design Patterns in Kotlin

Language features collapse most GoF boilerplate: `object` for singletons, data
classes + `copy` for builders/prototypes, sealed hierarchies + `when` for
visitor/state, higher-order functions for strategy/command/template method,
delegation for decorator/proxy.

### Creational

```kotlin
// Singleton
object Config { val url = System.getenv("URL") ?: "localhost" }

// Factory: companion invoke / factory functions / sealed dispatch
interface Parser { fun parse(s: String): Doc }
fun parserFor(kind: String): Parser = when (kind) {
    "json" -> JsonParser
    "csv" -> CsvParser
    else -> error("unknown $kind")
}
class Point private constructor(val x: Int, val y: Int) {
    companion object { operator fun invoke(x: Int = 0, y: Int = 0) = Point(x, y) }
}

// Builder: usually unnecessary — default + named args, or apply{}, or copy()
data class Request(val url: String, val method: String = "GET",
                   val headers: Map<String, String> = emptyMap())
Request("u", headers = mapOf("A" to "b"))
// DSL builder when construction is nested/validated:
fun request(block: RequestBuilder.() -> Unit) = RequestBuilder().apply(block).build()

// Prototype -> data class copy()
val staging = prodConfig.copy(host = "staging")

// Abstract Factory -> an interface of factory lambdas
class Deps(val repo: () -> Repo, val clock: () -> Long = System::currentTimeMillis)
```

### Structural

```kotlin
// Adapter -> extension functions, no wrapper class needed
fun LegacyUser.toDomain() = User(id = uid, name = "$first $last")

// Decorator -> class delegation, overriding only what changes
class RetryingRepo(private val inner: Repo) : Repo by inner {
    override suspend fun get(id: Int) = retry(3) { inner.get(id) }
}

// Proxy (lazy / access control)
class LazyImage(private val path: String) { val bitmap by lazy { decode(path) } }

// Facade -> a top-level function or a small object over a subsystem
fun sendReport(rows: List<Row>) { val pdf = render(rows); mailer.send(pdf) }

// Composite
sealed interface Node { val size: Int }
data class Leaf(val v: Int) : Node { override val size = 1 }
data class Branch(val children: List<Node>) : Node {
    override val size get() = children.sumOf { it.size }
}

// Flyweight -> object pool / cached companion factory; Bridge -> constructor injection
```

### Behavioral

```kotlin
// Strategy -> pass a function
fun sortUsers(users: List<User>, key: (User) -> Comparable<*>) = users.sortedBy { key(it) as Comparable<Any> }

// Template method -> abstract class with hooks, or an HOF with lambdas
suspend fun <T> transactional(block: suspend (Tx) -> T): T {
    val tx = begin()
    return try { block(tx).also { tx.commit() } } catch (e: Throwable) { tx.rollback(); throw e }
}

// Observer -> StateFlow / SharedFlow (or a simple listener list)
class Store {
    private val _state = MutableStateFlow(State())
    val state: StateFlow<State> = _state.asStateFlow()
    fun update(f: (State) -> State) { _state.update(f) }
}

// Command -> function type or sealed class of intents, undo via a stack
sealed interface Intent { data class Add(val text: String) : Intent; data object Undo : Intent }

// Chain of responsibility
fun chain(vararg handlers: (Req) -> Res?): (Req) -> Res? =
    { req -> handlers.firstNotNullOfOrNull { it(req) } }

// State -> sealed class + when (exhaustive transitions)
sealed interface Conn {
    data object Idle : Conn
    data class Open(val socket: Socket) : Conn
    data class Failed(val e: Throwable) : Conn
}
fun Conn.next(ev: Event): Conn = when (this) { … }

// Visitor -> sealed hierarchy + when (no double dispatch needed)
fun eval(n: Expr): Int = when (n) {
    is Num -> n.v
    is Add -> eval(n.l) + eval(n.r)
}

// Memento -> data class snapshots; Mediator -> a coordinator class owning the flows
// Iterator -> Iterable/Sequence + `operator fun iterator()`
```

### Architectural & Kotlin-specific

```kotlin
// Dependency injection = constructor parameters (Koin/Hilt only for wiring at the edges)
class UserService(private val repo: UserRepo, private val clock: Clock = Clock.System)

// Repository + Result-typed errors instead of exceptions
sealed interface Outcome<out T> {
    data class Ok<T>(val value: T) : Outcome<T>
    data class Err(val reason: String) : Outcome<Nothing>
}

// MVVM / MVI with StateFlow: state in, intents out, unidirectional data flow
// Use cases as invokable objects: class GetUser(...) { suspend operator fun invoke(id: Int) = … }
// Typed wrappers to kill primitive obsession: @JvmInline value class UserId(val raw: Long)
// Explicit API mode for libraries: kotlin { explicitApi() }
```

SOLID in Kotlin terms: small files with top-level functions (S); extensions and
sealed hierarchies to extend without editing (O); `final`-by-default keeps
substitution honest (L); narrow `fun interface`s / small interfaces (I);
constructor-inject interfaces or function types (D).

## 27. Idioms, Gotchas & Best Practices

```kotlin
val x = if (c) a else b                    // expression bodies over statements
data class Foo(val a: Int)                  // model data with data/value classes
sealed interface State                      // exhaustive `when` instead of else-chains
list.firstOrNull() ?: default               // Elvis over null checks
obj?.let { }                                // scoped null handling
Config().apply { … }                        // configure without a builder
x.takeIf { it.isValid() }
fun interface Handler { fun handle(e: E) }  // SAM-style lambda for your own interface
require/check/error/TODO                    // fail fast with intent
value class Meters(val v: Double)           // avoid primitive obsession
listOf(...).asSequence()                    // lazy pipelines for big data
```

Common traps:

- `!!` and `lateinit` used to dodge nullability design.
- Mutating a `val` collection: `val list = mutableListOf()` is still mutable —
  expose `List<T>`, keep `MutableList` private (`asStateFlow()`, `toList()`).
- Returning a `MutableList` from a public API (callers can mutate your state).
- `equals`/`hashCode` on `data class` only covers **primary constructor** properties;
  array properties compare by reference (use `contentEquals`).
- `data class` `copy()` bypasses `init` validation of the copied fields you don't pass.
- Extension functions don't override members and are statically resolved.
- `GlobalScope`, unstructured `launch`, and swallowing `CancellationException`.
- Blocking calls (`Thread.sleep`, JDBC, `File.readText`) inside coroutines without
  `Dispatchers.IO`/`withContext`.
- `runBlocking` in production code (deadlocks on limited dispatchers).
- Flow collected on the wrong dispatcher; `flowOn` affects **upstream** only.
- `it` shadowing in nested lambdas — name the parameters.
- `lazy` default is synchronized (cheap but not free); `by lazy` on a `var` is invalid.
- `object` initialization order and cyclic `object` references.
- Companion `const val` vs `val` (only `const` is inlined and usable in annotations).
- Integer division truncation; `Float`/`Double` equality; use `BigDecimal` for money.
- Java platform types silently allowing null; annotate boundaries.
- `open` classes and `init`-order calls to overridable members.
- Uncontrolled `inline` on large functions (bytecode bloat).
- Boxing `Int?`/generics in hot paths; use `IntArray` and primitive specializations.

Style: official Kotlin conventions (4 spaces, `camelCase`, `PascalCase` types,
`UPPER_SNAKE` consts, trailing commas, one class per file when large),
KDoc (`/** @param @return @throws @sample */`), `explicitApi()` for libraries,
prefer immutability, prefer expressions, keep functions short and pure.

## 28. One-Liners & Cookbook

```kotlin
// group and count
words.groupingBy { it.first() }.eachCount()
// group into map of lists
users.groupBy { it.dept }
// index by key
users.associateBy { it.id }
// unique preserving order
items.distinctBy { it.id }
// flatten
nested.flatten(); nested.flatMap { it }
// chunk
items.chunked(100).forEach { batch -> save(batch) }
// sum a field / average
orders.sumOf { it.total }; orders.map { it.total }.average()
// top-n
users.sortedByDescending { it.score }.take(10)
// multi-key sort
users.sortedWith(compareBy({ it.dept }, { -it.score }))
// map with index
items.mapIndexed { i, v -> "$i:$v" }
// partition
val (active, inactive) = users.partition { it.active }
// zip to map
keys.zip(values).toMap()
// safe cast + filter
anyList.filterIsInstance<String>()
// nullable chain with default
user?.address?.city ?: "unknown"
// string to enum safely
runCatching { Color.valueOf(s) }.getOrNull()
// swap
list[i] = list[j].also { list[j] = list[i] }
// measure time
val (value, dur) = measureTimedValue { work() }
// retry with backoff
suspend fun <T> retry(times: Int, block: suspend () -> T): T {
    repeat(times - 1) { i ->
        runCatching { return block() }
        delay(100L shl i)
    }
    return block()
}
// parallel map
coroutineScope { items.map { async { fetch(it) } }.awaitAll() }
// read a file lazily and count matches
File("app.log").useLines { lines -> lines.count { "ERROR" in it } }
// JSON round-trip
Json.decodeFromString<User>(Json.encodeToString(user))
// build a string
buildString { items.forEach { appendLine(it) } }
// range checks
if (x in 1..10) …; if (name in setOf("a", "b")) …
// singleton lazy service
val http by lazy { HttpClient() }
// exit code from main
fun main() { if (!ok) exitProcess(1) }
```

---

### Further reading

- Language docs: https://kotlinlang.org/docs/home.html
- Standard library API: https://kotlinlang.org/api/latest/jvm/stdlib/
- Coding conventions: https://kotlinlang.org/docs/coding-conventions.html
- Coroutines guide: https://kotlinlang.org/docs/coroutines-guide.html
- Kotlin Multiplatform: https://kotlinlang.org/docs/multiplatform.html
- Kotlin Playground: https://play.kotlinlang.org/
