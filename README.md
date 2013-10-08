Objective-C-style-guideline
===========================

This guideline is made by Huy Ares. Use to create a standard of code for my projects.

## Table of Contents

* [Comment](#comment)
* [Dot-Notation Syntax](#dot-notation-syntax)
* [Operators](#operators)
* [Types](#types)
* [Methods](#methods)
* [Pragma Mark and Implementation Organization](#pragma-mark-and-implementation-organization)
* [Control Structures](#control-structures)
* [Import](#import)
* [Properties](#properties)
* [Private Methods and Properties](#private-methods-and-properties)
* [Extern, Const and Static](#extern-const-and-static)
* [Naming](#naming)
* [Enums](#enums)
* [Exceptions and Error Handling](#exceptions-and-error-handling)
* [Blocks](#blocks)
* [Literals](#literals)
* [Categories](#categories)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Image Naming](#image-naming) 
* [Booleans](#booleans)
* [Singletons](#singletons)
* [Xcode Project](#xcode-project)

## Comment

* All method and properties **must** be comment with VVDocument

**Examples**
```objc
/**
 *  <#Description#>
 *
 *  @param block      <#block description#>
 *  @param parameters <#parameters description#>
 */
+ (void)getProduct:(void (^)(NSArray *ListProduct, NSObject *error))block parameters:(NSDictionary *)parameters;
```

* Write readable code to avoid comment in code as less as possible

## Dot-Notation Syntax

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**For example:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

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

* Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

* Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

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


## Extern, Const and Static

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

 * When methods return an error parameter by reference, switch on the returned value, not the error variable.

**For example:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).



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
 * Dictionary literals should have no space between the key and the colon, and a single space between colon and value.

``` objc
NSArray *theShit = @[@1, @2, @3 ;

NSDictionary *keyedShit = @{GHDidCreateStyleGuide: @YES};
```

 * `NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
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

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**For example:**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**Not:**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder.

## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Not:**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).


## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).
