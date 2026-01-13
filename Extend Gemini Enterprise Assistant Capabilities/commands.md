## 🌟 Extend Gemini Enterprise Assistant Capabilities – Full Command Guide 🌟

This guide provides the full sequence of commands to complete the lab: Extend Gemini Enterprise Assistant Capabilities.

---

### Command 1 — properties for OPEN API schema

This is properties for OPEN API schema.

```bash
properties:
  feedback:
    type: string
    description: Employee feedback text
    example: "The new scheduling system is much easier to use."
  store_number:
    type: integer
    description: Store number associated with the feedback
    example: 102


```

---

### Command 2 — Instruction For Playbook
```bash
- Greet the user.
- You should ask the user for their store number and feedback.
- Confirm that both are provided and valid, then use that information to call the ${TOOL: Record Employee Feedback}.
- After the feedback is successfully recorded, you should thank the user and let them know that their feedback has been saved.
```
