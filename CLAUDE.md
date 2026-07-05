# CLAUDE.md — Design Studio Bootstrap

This file tells Claude Code how to load and use Design Studio.

## Skill Loading

When the `design-studio` skill is invoked (via `/design-studio` or by matching a design-oriented request), load `skills/design-studio/SKILL.md` as the primary instruction set.

## Trigger Detection

Design Studio should engage when the developer's request matches any of:

- "design a..." / "help me think through..."
- "what's the best way to..."
- "review this code against the design"
- "plan the implementation for..."
- "should I use X or Y for..."
- Direct invocation: `/design-studio`

When triggered, follow the protocol in `skills/design-studio/SKILL.md` — assess the current stage, load the corresponding stage file, and begin the collaborative design process.

## Design Philosophy

Design Studio maximizes engineering thinking, not generated code. It helps developers think deeper, decide better, and design clearer — without writing the implementation.

**The developer owns the software. The AI owns the design conversation.**
