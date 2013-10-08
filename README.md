Objective-C-style-guideline
===========================

Most of these guidelines are to match Apple's documentation and community-accepted best practices. Some are derived some personal preference. This document aims to set a standard way of doing things so everyone can do things the same way. If there is something you are not particularly fond of, it is encouraged to do it anyway to be consistent with everyone else.

This document is mainly targeted toward iOS development, but definitely applies to Mac as well.

## Operators

```objective-c
NSString *foo = @"bar";
NSInteger answer = 42;
answer += 9;
answer++;
answer = 40 + 2;
```

The `++`, `--`, etc are preferred to be after the variable instead of before to be consistent with other operators. Operators separated should **always** be surrounded by **spaces** unless there is only one operand.


## Types

`NSInteger` and `NSUInteger` should be used instead of `int`, `long`, etc per Apple's best practices and 64-bit safety. `CGFloat` is preferred over `float` for the same reasons. This future proofs code for 64-bit platforms.

All Apple types should be used over primitive ones. For example, if you are working with time intervals, use `NSTimeInterval` instead of `double` even though it is synonymous. This is considered best practice and makes for clearer code.


## Methods

```objective-c
- (void)someMethod {
    // Do stuff
}


- (NSString *)stringByReplacingOccurrencesOfString:(NSString *)target withString:(NSString *)replacement {
    return nil;
}
```

* There should **always** be a space between the `-` or `+` and the return type (`(void)` in this example). There should **never** be a space between the return type and the method name.

* There should **never** be a space before or after colons `(` `)`. If the parameter type is a pointer, there should **always** be a space between the class and the `*`.

* There should **always** be a space between the end of the method and the opening bracket `{`. The opening bracket should **never** be on the following line.

* There should **always** be `2` new lines between methods. This matches some Xcode templates (although they change a lot) and increase readability.


## Pragma Mark and Implementation Organization

An expect of a UIView:

```objc
#pragma mark Properties

@dynamic someProperty;

...

- (void)setCustomProperty:(id)value {}

#pragma mark Lifecycle

+ (id)objectWithThing:(id)thing {}
- (id)init {}

#pragma mark Drawing

- (void)drawRect:(CGRect) {}

#pragma mark Another functional grouping

#pragma mark GHSuperclass

- (void)someOverriddenMethod {}

#pragma mark NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark NSObject

- (NSString *)description {}
```

* Methods should be grouped by inheritance. In the above example, if some `UIResponder` methods were used, they should go between the `NSObject` and `UIView` methods since that's where they fall in the inheritance chain.


## Control Structures

* There should **always** be a `space` after the control structure (i.e. `if`, `else`, `switch`, `while`, `for`, `for in`, etc).


### If/Else

```objective-c
if (button.enabled) {
    // Stuff
} else if (otherButton.enabled) {
    // Other stuff
} else {
    // More stuf
}
```

* `else` statements should begin on the same line as their preceding `if` statement.
    
```objective-c
// Comment explaining the conditional
if (something) {
    // Do stuff
}

// Comment explaining the alternative
else {
    // Do other stuff
}
```

* If comments are desired around the `if` and `else` statement, they should be formatted like the example above.


### Switch

```objective-c
switch (something.state) {
    case 0: {
        // Something
        break;
    }
    
    case 1: {
        // Something
        break;
    }
    
    case 2:
    case 3: {
        // Something
        break;
    }
    
    default: {
        // Something
        break;
    }
}
```

* Brackets `{ }` are desired around **each case**. If multiple cases are used, they should be on **separate lines**. `default` should **always** be the last case and should **always** be included.


### For

```objective-c
for (NSInteger i = 0; i < 10; i++) {
    // Do something
}


for (NSString *key in dictionary) {
    // Do something
}
```

* When iterating using integers, it is preferred to start at `0` and use `<` rather than starting at `1` and using `<=`. Fast enumeration is generally preferred.


### While

```objective-c
while (something < somethingElse) {
    // Do something
}
```

## Import

* **Always** use `@class` whenever possible in header files instead of `#import` since it has a slight compile time performance boost.

From the [Objective-C Programming Guide](http://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ObjectiveC/ObjC.pdf) (Page 38):

> The @class directive minimizes the amount of code seen by the compiler and linker, and is therefore the simplest way to give a forward declaration of a class name. Being simple, it avoids potential problems that may come with importing files that import still other files. For example, if one class declares a statically typed instance variable of another class, and their two interface files import each other, neither class may compile correctly.

### Header Prefix

* Adding frameworks that are used in the majority of a project to a header prefix is preferred. If these frameworks are in the header prefix, they should **never** be imported in source files in the project.

* For example, if a header prefix looks like the following:

```objective-c
#ifdef __OBJC__
    #import <Foundation/Foundation.h>
    #import <UIKit/UIKit.h>
#endif
```

`#import <Foundation/Foundation.h>` should never occur in the project outside of the header prefix.

## Properties

```objective-c
@property (nonatomic, strong) UIColor *topColor;
@property (nonatomic, weak) UIView *topView;
@property (nonatomic, assign) CGSize shadowOffset;
@property (nonatomic, retain, readonly) UIActivityIndicatorView *activityIndicator;
@property (nonatomic, assign, getter=isLoading) BOOL loading;
```

* If the property is `nonatomic`, it should be first. The next option should **always** be `strong`, `weak` or `assign` since if it is omitted, there is a warning. `readonly` should be the next option if it is specified. `readwrite` should never be specified in header file. `readwrite` should only be used in class extensions. `getter` or `setter` should be last. `setter` should rarely be used.

* See an example of `readwrite` in the *Private Methods* section.


## Private Methods and Properties

MyShoeTier.h

```objective-c
@interface MyShoeTier : NSObject <Protocal1, Protocal2, Protocal3, ...>

// Public method will be declare in .h file
- (void)publicMethod:(NSInteger)number;

@property (nonatomic, strong, readonly) MyShoe *publicShoe;
@property (nonatomic, weak) NSObject<Protocol> *object;
...

@end
```

MyShoeTier.m

```objective-c
#import "MyShoeTier.h"

@interface MyShoeTier ()

// Private method will be declare in .m file
- (void)_crossLace:(MyLace *)firstLace withLace:(MyLace *)secondLace;

@property (nonatomic, strong, readwrite) MyShoe *privateShoe;
@property (nonatomic, strong) NSMutableArray *laces;

@end

@implementation MyShoeTier

// This is a private method
- (void)_crossLace:(MyLace *)firstLace withLace:(MyLace *)secondLace {
    
    // Use self + dot when access public properties
    self.publicShoe = nil;

    // Use `_` when access private properties
    _privateShoe = nil;
}

@end
```

* Private method **always** have a `_` before method name, and will be declared in **.h** file.

* Private methods should **always** be created in a class extension for simplicity since a named category can't be used if it adds or modifies any properties.

Note: The above example provides an example for an acceptable use of a `readwrite` property.


## Extern, Const, and Static

```objective-c
extern NSString *const kMyConstant;
extern NSString *MyExternString;
static NSString *const kMyStaticConstant;
static NSString *staticString;
```

## Naming

* In general, everything should be prefixed with a 2-3 letter prefix. Longer prefixes are acceptable, but not recommended.

* It is a good idea to prefix classes with an application specific application if it is application specific code. If there is code you plan on using in other applications or open sourcing, it is a good idea to do something specific to your or your company for the prefix.

* If you company name is *Awesome Buckets* and you have an application named *Bucket Hunter*, here are a few examples:

```objective-c
ABLoadingView // Simple view that can be used in other applications
BHAppDelegate // Application specific code
BHLoadingView // `ABLoadingView` customized for the Bucket Hunter application
```

## Enums

```objective-c
enum {
    Foo,
    Bar
};

typedef enum {
    SSLoadingViewStyleLight,
    SSLoadingViewStyleDark
} SSLoadingViewStyle;

typedef enum {
    SSHUDViewStyleLight = 7,
    SSHUDViewStyleDark = 12
} SSHUDViewStyle;
```

## Exceptions and Error Handling

 * Don't use exceptions for flow control.
 * Use exceptions only to indicate programmer error.
 * To indicate errors, use an `NSError **` argument or send an error on a [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) signal.

## Blocks

 * Blocks should have a space between their return type and name.
 * Block definitions should omit their return type when possible.
 * Block definitions should omit their arguments if they are `void`.
 * Parameters in block types should be named unless the block is initialized immediately.

```objective-c
typedef void (^BlockName)(FMClass *response, NSError *error);
```

```objc
void (^blockName1)(void) = ^{
    // do some things
};

id (^blockName2)(id) = ^ id (id args) {
    // do some things
};
```

## Literals

 * Avoid making numbers a specific type unless necessary (for example, prefer `5` to `5.0`, and `5.3` to `5.3f`).
 * The contents of array and dictionary literals should have a space on both sides.
 * Dictionary literals should have no space between the key and the colon, and a single space between colon and value.

``` objc
NSArray *theShit = @[ @1, @2, @3 ];

NSDictionary *keyedShit = @{ GHDidCreateStyleGuide: @YES };
```

 * Longer or more complex literals should be split over multiple lines (optionally with a terminating comma):

``` objc
NSArray *theShit = @[
    @"Got some long string objects in here.",
    [AndSomeModelObjects too],
    @"Moar strings."
];

NSDictionary *keyedShit = @{
    @"this.key": @"corresponds to this value",
    @"otherKey": @"remoteData.payload",
    @"some": @"more",
    @"JSON": @"keys",
    @"and": @"stuff",
};
```

## Categories

 * Categories should be named for the sort of functionality they provide. Don't create umbrella categories.
 * Category methods should always be prefixed.
 * If you need to expose private methods for subclasses or unit testing, create a class extension named `Class+Private`.

