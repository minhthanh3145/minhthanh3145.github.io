---
title: "Revisiting the Visitor Pattern to design resilient software library"
date: "2020-04-25"
categories:
  - "algorithm"
  - "architecture"
  - "software-development"
layout: post
subclass: post
navigation: "true"
cover: "assets/images_1/undraw_creative_team_r90h.png"
---

The visitor pattern is a behavior design pattern, which means it presents a scheme for objects to work together towards solving a problem. **The problem the visitor pattern solves is to add functionalities to a class hierarchy without having to modify every class**. We will examine this pattern in the context of designing software library.

## Designing shapes

The problem we will use for this post is, without a doubt, very platitudinous: How to design a hierarchy of objects to represent shapes with certain computable aspects like area and volume.

![](https://dafuqisthatblog.files.wordpress.com/2020/04/download-1.png?w=180)

[Did someone say my name ?](https://vi.wikipedia.org/wiki/Th%C3%BA_m%E1%BB%8F_v%E1%BB%8Bt_Perry)

I often find such problems to be too unrealistic, and therefore delude the purpose of design patterns, but sometimes platitudes may be analyzed in helpful ways.

Let's say you're not mathematically well-versed, so you only know properties of shapes (rectangles, circles, etc) and the formula to calculate their area by reading the requirements. Also assume that all relevant requirements don't arrive at the same time, but are scattered through time and space, under the influence of multiple parties. It would be more realistic that way.

So as you are looking melancholically at your female co-worker, the first requirements comes.

![](https://dafuqisthatblog.files.wordpress.com/2020/04/undraw_creative_team_r90h.png?w=1024)

[https://undraw.co/](https://undraw.co/)

It is to calculate the area of the shapes using their respective attributes and formula.

## A solution without visitor pattern

A no-brainer solution is the classic hierarchy of shapes, with concrete classes such as Circle and Square extending the base class Shape. The computations, which return literal values or simplicity sake, are put into concrete classes.

public class WithoutVisitorPattern {

    public abstract static class Shape {
        public abstract int getArea();
    }

    public static class Rectangle extends Shape {
        @Override
        public int getArea() {
            return 0;
        }
    }

    public static class Circle extends Shape {
        @Override
        public int getArea() {
            return 1;
        }
    }

    public static class Shape1 extends Shape {
        @Override
        public int getArea() {
            return 2;
        }
    }

    public static class Shape2 extends Shape {
        @Override
        public int getArea() {
            return 3;
        }
    }

    public static void main(String\[\] args) {
        List<Shape> shapes = new ArrayList<>();
        shapes.add(new Rectangle());
        shapes.add(new Circle());
        shapes.add(new Shape1());
        shapes.add(new Shape2());
        System.out.println(calculateShapeArea(shapes));
    }

    public static int calculateShapeArea(List<Shape> shapes) {
        int result = 0;
        for(Shape shape : shapes) {
            result += shape.getArea();
        }
        return result;
    }
}

It works well. With the current scope of requirements, any more complexity would be unnecessary.

But life isn't that simple. Your team decide to distribute the above code into production. This means that the code will live inside a version of your distributed software library, since we're talking in context of library design. You distribute your library as a `JAR` file to your clients. Your clients use this library to calculate the area of the shapes to assist in mission-critical computations.

As they use it, their need expands beyond area, and thus they demand that your library accommodates other computations as well, such as volume. For the no-brainer solution, we need to add a new method called `getVolume` and its implementation to every shape in the hierarchy.

Let's say your team supports an extensive library for shapes, so there are more than 23 shapes in the hierarchy, you would need to add 23 `getVolume` methods into each of these classes.

## Why adding functionalities without modifying the class hierarchy?

This is the problem I talked about at the beginning of the post, how to add additional functionalities relevant to the class hierarchy without modifying the class hierarchy. You may argue that you'd have to add 23 different `getVolume` methods either way, because the formula for each of these 23 shapes is different from each other, and these methods must be implemented somewhere.

One of the important characteristics of good software design is to accommodate changes. We will see why the current design doesn't do that very well.

### Other software depend on your software

Let's imagine another software team is working on a different project and they found your library to be very useful, but lacking some shapes that are particular to their projects. So they use the `JAR` file you distribute, extend the subclass `Shape` and implement additional shapes that are relevant to their project.

Then the requirement for adding methods to calculate volumes arrives, among other requirements such as bug fixes and optimizations. Following the current no-brainer design, you simply add 23 `getVolume` methods to each of the class in the hierarchy and release the new version of the library with attractive bug fixes and optimizations.

However, because the software team above also extends from your base class, they would also need to add `getVolume` into all of their shapes, if they want to upgrade to the latest version to benefit from all that bug fixes and optimizations. Such efforts tend to lead to the decision to not update libraries. For you, the library designer, this means that people are not receiving values from your work. What's the point of updating a library if it's not to continue to provide values ?

This gets worse when the change is cascaded to many software teams, all of whom depend on your library. We see that the current no-brainer design does not accommodate changes very efficiently.

### Software ceases to evolve

It is not an exception that software stops being maintained. They may stop because the budget runs out so no one is maintaining the library anymore. They may stop because of a natural disaster that kills everyone who works on that library. Whatever the cause is, the consequence is that the original library developers can no longer accommodate new changes to the library. That responsibility unfortunately will fall into other software teams who extend your library.

New functionalities can no longer be added directly into the class hierarchy, since the hierarchy exists within the library and the library is no longer maintained. The software team who decides to continue using your library must either find another library or writing their own thing.

## Making your library resilient

So after we have seen that adding functionalities by directly modifying code of the hierarchy is not a design that would facilitate useful, long-lasting software libraries. The solution to this problem is to create another class hierarchy and delegate the computations to it.

public class PreVisitorPattern {

    public abstract static class Shape {
        public abstract int getArea(AreaCalculator areaCalculator);
    }

    public static class AreaCalculator {
        public int getAreaOf(Rectangle shape) {
            return 0;
        }
        public int getAreaOf(Circle shape) {
            return 1;
        }
        public int getAreaOf(Shape1 shape) {
            return 2;
        }
        public int getAreaOf(Shape2 shape) {
            return 3;
        }
    }

    public static class Rectangle extends Shape {
        @Override
        public int getArea(AreaCalculator areaCalculator) {
            return areaCalculator.getAreaOf(this);
        }
    }

    public static class Circle extends Shape {
        @Override
        public int getArea(AreaCalculator areaCalculator) {
            return areaCalculator.getAreaOf(this);
        }
    }

    public static class Shape1 extends Shape {
        @Override
        public int getArea(AreaCalculator areaCalculator) {
            return areaCalculator.getAreaOf(this);
        }
    }

    public static class Shape2 extends Shape {
        @Override
        public int getArea(AreaCalculator areaCalculator) {
            return areaCalculator.getAreaOf(this);
        }
    }

    public static void main(String\[\] args) {
        List<Shape> shapes = new ArrayList<>();
        shapes.add(new Rectangle());
        shapes.add(new Circle());
        shapes.add(new Shape1());
        shapes.add(new Shape2());
        System.out.println(calculateShapeAttribute(shapes));
    }

    public static int calculateShapeAttribute(List<Shape> shapes) {
        AreaCalculator areaCalculator = new AreaCalculator();
        int result = 0;
        for(Shape shape : shapes) {
            result += shape.getArea(areaCalculator);
        }
        return result;
    }
}

This solution creates a class called `AreaCalculator` which has a method to compute area of a given shape. Each call to `getArea` is now given an instance of `AreaCalculator`. Here the instance `AreaCalculator` is then given the access to the shape and vary the logic to compute area correspondingly.

### Avoid the temptation to group methods together to reduce ostensible duplicates

Looking at the class `AreaCalculator`, one reasonable idea is to instead write it like this to avoid duplicates.

    public abstract static class Shape {
        public int getArea(AreaCalculator areaCalculator) {
            return areaCalculator.getAreaOf(this);
        }
    }

But the instance `this` is of type `Shape` which the `AreaCalculator` does not have a method that accepts, so let's say we modify `AreaCalculator` to:

        public int getAreaOf(Shape shape) {
            if(shape instanceof Rectangle) {
                return aPrivateMethodToCalculatAreaForRectangle(shape);
            }
            if(shape instanceof Shape1) {
                
            }
            // 21 more to go :(
        }

But this defeats the purpose. Suppose that another software team want to extend `AreaCalculator` because they have a more optimized way to calculate area of a Rectangle. They wouldn't be able to just do that just by extending `AreaCalculator`, because they'd have to handle computations for other shapes within the method of `getAreaOf` . This is why we'd prefer to have each `getAreaOf` method for each concrete class extending `Shape`.

### Avoid the temptation to inject instances into constructor to reduce ostensible duplicates

The second problem is that, each method is receiving an `AreaCalculator` instance. This seems unnecessary, can we just inject the instance into the class constructor and then re-use it ? If we start with the base class, then we can write something like this:

    public abstract static class Shape {
        protected AreaCalculator calculator;
        Shape(AreaCalculator calculator) {
            this.calculator = calculator;
        }
        public abstract int getArea(AreaCalculator areaCalculator);
    }

Wait a minute, there are red marks !

![](https://dafuqisthatblog.files.wordpress.com/2020/04/screen-shot-2020-04-26-at-1.56.19-am.png?w=1024)

Turns out that, we have to add a constructor for all classes extending `Shape` in order to do it this way.

If we decide to give an instance `AreaCalculator` to all shapes in the constructor, it implies that we **have to construct an instance of `AreaCalculator`** before we can use any shape. The construction of an `AreaCalculator` instances may be computationally costly. For example, when such an `AreaCalculator` instance is constructed, the implementation could read CPU resources, get information from other parts of the application in order to determine the most efficient area computation strategy.

Our `Shape` hierarchy represents a set of lightweight objects that contain information about the shapes at hand. If we have to construct a `AreaCalculator` instance before a shape can be used, then we have to create potentially very costly object that aren't even used yet. We can't make the assumption that the area computation would be used right after these shapes are created, or is ever used during a lifetime of a Shape object at all. Therefore, delaying the construction of `AreaCalculator` right to the moment when the shapes need it, is the optimal strategy.

## The Visitor pattern

With all that said, the visitor pattern is actually half-way achieved. In order to support volume computation, we can just create a method `getVolume` in `VolumeCalculator`. But notice that `AreaCalculator` and `VolumeCalculator` actually shares the same signatures, and they are used in the same way, which means they can be combined through inheritance. The result is a new class hierarchy which represents computations which use the information of the `Shape` objects. This new hierarchy has the base class `Calculator` which has a method `calculate` that is used by `getArea` and `getVolume`.

Notice again that as you do this, `getVolume` and `getArea` now also has the same signature and logic: Receiving a `Calculator` instance, call `Calculator.calculate(this)` and return the result, therefore they can be combined into just one method in the class `Shape` hierarchy. Let's this method be called `getComputedValueFrom(Calculator)`.

If we replace `Calculator` by `Visitor`, `getComputedValueFrom` with `accept`, and `calculate` method with `visit`. then you end up with the visitor pattern.

public class VisitorPattern {

    public static abstract class ShapeVisitor {
        public abstract int visit(Rectangle shape);
        public abstract int visit(Circle shape);
        public abstract int visit(Shape1 shape);
        public abstract int visit(Shape2 shape);
    }

    public static class AreaShapeVisitor extends  ShapeVisitor {
        public int visit(Rectangle shape) { return 0;}
        public int visit(Circle shape) { return 1;}
        public int visit(Shape1 shape) { return 2;}
        public int visit(Shape2 shape) { return 3;}
    }

    public static class VolumeShapeVisitor extends ShapeVisitor {
        public int visit(Rectangle shape) { return -0;}
        public int visit(Circle shape) { return -1;}
        public int visit(Shape1 shape) { return -2;}
        public int visit(Shape2 shape) { return -3;}
    }

    public abstract static class Shape {
        public abstract int accept(ShapeVisitor visitor);
    }

    public static class Rectangle extends Shape {
        @Override
        public int accept(ShapeVisitor visitor) {
            return visitor.visit(this);
        }
    }

    public static class Circle extends Shape {
        @Override
        public int accept(ShapeVisitor visitor) {
            return visitor.visit(this);
        }
    }

    public static class Shape1 extends Shape {
        @Override
        public int accept(ShapeVisitor visitor) {
            return visitor.visit(this);
        }
    }

    public static class Shape2 extends Shape {
        @Override
        public int accept(ShapeVisitor visitor) {
            return visitor.visit(this);
        }
    }

    public static void main(String\[\] args) {
        List<Shape> shapes = new ArrayList<>();
        shapes.add(new Rectangle());
        shapes.add(new Circle());
        shapes.add(new Shape1());
        shapes.add(new Shape2());
        ShapeVisitor visitor = new VolumeShapeVisitor();
        System.out.println(calculateShapeAttributes(visitor, shapes));
    }

    public static int calculateShapeAttributes(ShapeVisitor visitor, List<Shape> shapes) {
        int result = 0;
        for(Shape shape : shapes) {
            result += shape.accept(visitor);
        }
        return result;
    }
}

## Conclusion

The visitor pattern solves the problem of adding functionality to a class hierarchy without modifying the code of the hierarchy itself. This requirement is necessary if we want to create software library that last. The derivation of this design pattern also reminds us of the design concerns that need to be taken into account.
