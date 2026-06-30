# Tesis
## Transformar espacios de estado 
Tenemos la función de transferencia: 

$$
G(s)= \frac{0.29s + 0.2407}{s^2 + 1.95s + 0.9296}
$$

Se usa el siguiente código: 
```Python
import control as ct

# Define a transfer function: H(s) = (2s + 10) / (s^2 + 5s + 4)
# Coefficients are ordered from highest power to lowest
num = [0.29, 0.2407]
den = [1, 1.95, 0.9296]

# Step 1: Create the transfer function object
sys_tf = ct.tf(num, den)

# Step 2: Convert to state-space using ss() or tf2ss()
sys_ss = ct.ss(sys_tf)

# View the individual matrices
print("Matrix A:\n", sys_ss.A)
print("Matrix B:\n", sys_ss.B)
print("Matrix C:\n", sys_ss.C)
print("Matrix D:\n", sys_ss.D)

```
