+++
title = 'Open Closed Principle'
date = '2025-12-16'
draft = false
#author = 'adriano'
tags = ['dev']
header_image = "/images/open_closed.png"
+++

In modern software development where requirements shift faster than sprints, the biggest cost isn’t writing code, it’s changing it. Every modification to existing logic carries risk: a new feature shouldn’t break last quarter’s production fix. Yet too often, we find ourselves reopening classes, tweaking if-chains, and nervously re-testing everything just to add a tiny new rule. In this article, we’ll walk through a realistic Java example bonus calculation logic and refactor it step by step: from a fragile, conditional heavy method to a flexible, strategy driven design that embraces OCP.

Open Close Principle from SOLID principles states that:

`Software entities should be open for extension, but closed for modification`

At first glance, it sounds paradoxical. How can something be both open and closed? The answer lies not in rigid dogma, but in thoughtful design:
- **Closed for Modification**: Core behavior remains stable, tested, and untouched.
- **Open for Extension**: New functionality is added by extending the system, not rewriting it. Let's refactor some code until we dive in into the OCP.

Take this Java code as an example:

```Java
public class BonusSalary {

    public double calc(double salary, String performanceCategory, PersonalInfo info) {

        double bonus = 0;

        if (performanceCategory.equals("poor"))
            bonus += Salary * 0.1;
        else if (performanceCategory.equals("medium"))
            bonus += Salary * 0.5;

        if (info.hasChildren) {
            bonus += 500 * info.numberOfChildren;
        }

        return bonus;
    }
}
```

```Java
public class PersonalInfo {
    public boolean hasChildren;
    public int numberOfChildren;
}
```

What can we refactor here?

- Remove the nested ifs
- There are two responsibilities here in the same function: bonus calculation by performance and bonus calculation by number of children's
- hasChildren is not needed because numberOfChildren is enough
- Use an enum to map categories (added two more to show the usefulness of throwing exceptions)

We end up with this:
```Java
public enum PerformanceCategory {
    POOR, MEDIUM, GOOD, EXCEPTIONAL
}
```
```Java
public class BonusSalary {

     public double calc(double salary, PerformanceCategory performanceCategory, PersonalInfo info) {

        double bonus = 0;

        bonus += this.getPerformanceBonus(salary, performanceCategory);
        bonus += this.getChildrenBonus(info.numberOfChildren);

        return bonus;
    }

     private double getPerformanceBonus(double salary, PerformanceCategory performanceCategory) {

        return switch (performanceCategory) {
            case POOR -> salary * 0.1;
            case MEDIUM -> salary * 0.5;
            case EXCEPTIONAL -> salary;
            default -> throw new IllegalArgumentException("Unknown performance category: " + performanceCategory);
        };
    }

    private double getChildrenBonus(int numberOfChildren) {
         if (numberOfChildren < 0) return 0; 
        return numberOfChildren * 500;
    }
}
```
If we want a more decoupled solution we can go with Strategy Pattern respecting that way the OCP. First we create a contract that all the bonus logic should respect:
```Java
public interface BonusStrategy {
    double calculate(double salary, PersonalInfo info, PerformanceCategory perfCat);
}
```
Then we can have implementations of that contract:
```Java
public class PerformanceBonusStrategy implements BonusStrategy {
    @Override
    public double calculate(double salary, PersonalInfo info, PerformanceCategory category) {
        return switch (category) {
            case POOR -> salary * 0.1;
            case MEDIUM -> salary * 0.5;
            case EXCEPTIONAL -> salary;
            default -> throw new IllegalArgumentException("Unknown performance category: " + category);
        };
    }
}
```
```Java
public class ChildrenBonusStrategy implements BonusStrategy {
    @Override
    public double calculate(double salary, PersonalInfo info, PerformanceCategory perfCat) {

        if (info.numberOfChildren < 0) {
            return 0;
        }

        return info.numberOfChildren * 500;
    }
}
```
Then we can use those implementations to perform calculations in a different place:
```Java
public record BonusCalculator(List<BonusStrategy> strategies) {
    public double calculateTotalBonus(double salary, PersonalInfo info, PerformanceCategory perfCat) {
        return strategies.stream()
                .mapToDouble(strategy -> strategy.calculate(salary, info, perfCat))
                .sum();
    }
}
```
```Java
 public double calc(double salary, PerformanceCategory performanceCategory, PersonalInfo info) {

    var calculator = new BonusCalculator(List.of(
            new PerformanceBonusStrategy(),
            new ChildrenBonusStrategy()
    ));

    return calculator.calculateTotalBonus(salary, info, performanceCategory);
}
```
It is now

- **Open for Extension**: You should be able to add new functionality or behaviors to the system.
- **Closed for Modification**: Adding these new features should not require changing the existing, tested, and stable source code.

With this we:

- **Reduced the Risk of Bugs**: Since you aren't touching existing code, you minimize the chance of breaking current features (regressions).
- **Easier Maintenance**: Systems become more modular, allowing teams to work on new features in isolation.
- **Improved Scalability**: New requirements can be "plugged in" without a total redesign of the core system.
- **Improved Testability through Decoupling**: OCP forces developers to decouple components. Decoupled units are inherently easier to put under test because they have fewer hidden dependencies and clear entry/exit points (extension points).

OCP isn’t free: it introduces more classes/interfaces and some runtime overhead. Use it where volatility is expected (e.g., business rules that change often). Not every if needs a strategy!

By applying the Open Closed Principle, we transformed a fragile, monolithic method into a modular system. New bonus rules can now be added as independent Bonus Strategy implementations and no modification of existing, tested code is needed. This reduces regression risk, simplifies testing (strategies can be unit tested in isolation), and allows teams to extend functionality safely. Remember: OCP is not about avoiding change, but about channeling change through extension points, interfaces, abstractions, and composition so evolution doesn’t break stability.