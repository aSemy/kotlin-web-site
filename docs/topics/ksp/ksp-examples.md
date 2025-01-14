[//]: # (title: KSP examples)

## Get all member functions

```kotlin
fun KSClassDeclaration.getDeclaredFunctions(): List<KSFunctionDeclaration> {
    return this.declarations.filterIsInstance<KSFunctionDeclaration>()
}
```

## Check whether a class or function is local

```kotlin
fun KSDeclaration.isLocal(): Boolean {
    return this.parentDeclaration != null && this.parentDeclaration !is KSClassDeclaration
}
```

## Find the actual class or interface declaration that the type alias points to

```kotlin
fun KSTypeAlias.findActualType(): KSClassDeclaration {
    val resolvedType = this.type.resolve().declaration
    return if (resolvedType is KSTypeAlias) {
        resolvedType.findActualType()
    } else {
        resolvedType as KSClassDeclaration
    }
}
```

## Collect suppressed names in a file annotation

```kotlin
// @file:kotlin.Suppress("Example1", "Example2")
fun KSFile.suppressedNames(): List<String> {
    val ignoredNames = mutableListOf<String>()
    annotations.forEach {
        if (it.shortName.asString() == "Suppress" && it.annotationType.resolve()?.declaration?.qualifiedName?.asString() == "kotlin.Suppress") {
            it.arguments.forEach {
                (it.value as List<String>).forEach { ignoredNames.add(it) }
            }
        }
    }
    return ignoredNames
}
```