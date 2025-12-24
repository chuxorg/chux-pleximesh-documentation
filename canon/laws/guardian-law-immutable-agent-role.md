# Guardian Law: Immutable Agent Role

## Problem Statement
Agents must not be able to change roles during their lifecycle. Allowing role mutation introduces ambiguity, privilege escalation risk, and invalidates governance assumptions.

## Law Definition
1. **Role Assignment**  
   - An agent SHALL be assigned exactly one role at instantiation time by the Agent Factory.

2. **Role Immutability**  
   - Once assigned, an agent’s role MUST NOT change for the duration of its lifecycle.

3. **Enforcement**  
   - Role immutability MUST be enforced by the Agent Factory.  
   - Guardian MUST verify role immutability before authorizing task execution.  
   - Any attempt to mutate an agent’s role MUST result in immediate halt and escalation.

4. **Scope**  
   - This law applies to Engineering Agents, Guardian, PM, Voodoo, and any future agent class instantiated by the runtime.

5. **Escalation**  
   - If role ambiguity or mutation is detected, execution MUST halt and escalate to Human. Autonomous continuation is forbidden under role uncertainty.
