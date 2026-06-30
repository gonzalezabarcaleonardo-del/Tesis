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

Esto da como salida: 

```
Matrix A:
 [[-1.95   -0.9296]
 [ 1.      0.    ]]
Matrix B:
 [[1.]
 [0.]]
Matrix C:
 [[0.29   0.2407]]
Matrix D:
 [[0.]]
```

Esto lo interpretamos como: 

$$
\frac{dx_1}{dt}=-1.95x_1 - 0.9296x_2 + u
$$

$$
\frac{dx_2}{dt}=x_1
$$

$$
y = 0.29x_1 + 0.2407x_2
$$

Esto lo ponemos como la función en Julia:

```Julia
function (y, u)
x1=y[1]
x2=y[2] 
 dx1_dt = -1.95*x1 - 0.9296*x2 + u
 dx2_dt = x1
 return[dx1_dt, dx2_dt]
end 
```

El artículo de Runge-Kutta está [aquí](https://github.com/gonzalezabarcaleonardo-del/Tesis/blob/main/goeken.pdf)

Las ecuaciones utlizadas en el método de Runge Kutta de quinto orden son las siguientes:

$$
y_n+1 = y_n + b_1k_1 + b_2k_2 + b_3k_3 + b_4k_4
$$

$$

$$

$$

$$

$$

$$

$$
