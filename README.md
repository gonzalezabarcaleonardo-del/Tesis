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
function sistema3(t, y, u)
   x1=y[1]
   x2=y[2] 
   dx1_dt = -1.95*x1 - 0.9296*x2 + u
   dx2_dt = x1
   return [dx1_dt, dx2_dt]
end 
```

El artículo de Runge-Kutta está [aquí](https://github.com/gonzalezabarcaleonardo-del/Tesis/blob/main/goeken.pdf)

Las ecuaciones utlizadas en el método de Runge Kutta de quinto orden son las siguientes:


$$
y_{n+1} = y_n + h\sum_{i=1}^{s} b_ik_i
$$

Donde:

$$
k_i = f(t_n + C_ih, y_n + \sum_{j=1}^{s} a_{ij}k_j)
$$


$$
k_3 = hf(y_n - \frac{152}{125}k_1 + \frac{252}{125}k_2 - \frac{44}{125} hf_yk_1)
$$


$$
k_4 = hf(y_n + \frac{19}{2}k_1 - \frac{72}{7}k_2 + \frac{25}{14}k_3 + \frac{5}{2}hf_yk_1)
$$


$$
y_n+1 = y_n + \frac{5}{48}k_1 + \frac{27}{56}k_2 + \frac{125}{336}k_3 + \frac{1}{24}k_4
$$


$$
y_{n+1} = y_n + b_1k_1 + b_2k_2 + b_3k_3 + b_4k_4
$$


$$
k_1 = hf(y_n)
$$


$$
k_2 = hf(y_n + a_21k_1 + ha_22f_y(y_n)k_1)
$$


$$
k_3 = hf(y_n + a_31k_1 + a_32k_2 + ha_33f_y(y_n)k_1)
$$


$$
k_4 = hf(y_n + a_41k_1 + a_42k_2 + a_43k_3 + ha_44f_y(y_n)k_1)
$$


$$
b_1 + b_2 + b_3 + b_4 = 1
$$


La función Rungue-Kutta es la siguiente:

```Julia
function RK5(f, h, t, y_n, u)
   k_1 = f(t, y_n, u)
   k_2 = f(t + h/3, y_n + (h*k_1)/3, u)
   k_3 = f(t + 2*h/5, y_n + h*(4*k_1/25+6*k_2/25), u)
   k_4 = f(t + h, y_n + h*(k_1/4 - 3*k_2 + 15*k_3/4), u)
   k_5 = f(t + 2*h/5, y_n + h*(2*k_1/27 + 10k_2/9 - 50*k_3/81 + 8*k_4/81), u)
   k_6 = f(t + 4*h/5, y_n +h*(2*k_1/25 + 12*k_2/25 + 2*k_3/15 + 8*k_4/75), u)
   y_np1 = y_n + h*(23*k_1/192 +125*k_3/192 - 27*k_5/64 + 125*k_6/292)
   return y_np1 
end

```

---

# Controladores 
## Controlador PID

La ecuación del controlador PID es la siguiente: 

$$
U(t) = k_p e(t) + k_i \int e(t) dt + k_d \frac{de(t)}{dt} 
$$


