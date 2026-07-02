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


$$
y_{n+1} = y_n + h(b_1k_1 + b_2k_2 + b_3k_3 + b_4k_4 + b_5k_5 + b_6k_6)
$$


$$
y_{n+1} = y_n + h(\frac{23k_1}{192} + \frac{125k_3}{192} - \frac{27k_5}{64} + \frac{125k_6}{192}) 
$$


Donde:

$$
k_i = f(t_n + C_ih, y_n + \sum_{j=1}^{s} a_{ij}k_j)
$$

Se obtienen los valores de k de la siguiente manera:

$$
k_1 = f(t_n + C_1h, y_n + h(a_{11}k_1 + a_{12}k_2 + a_{13}k_3 + a_{14}k_4 + a_{15}k_5 + a_{16}k_6))
$$


$$
k_1 = f(t_n, y_n )
$$


$$
k_2 = f(t_n + C_2h, y_n + h(a_{21}k_1 + a_{22}k_2 + a_{23}k_3 + a_{24}k_4 + a_{25}k_5 + a_{26}k_6))
$$


$$
k_2 = f(t_n + \frac{h}{3}, y_n + \frac{h}{3}k_1)
$$


$$
k_3 = f(t_n + C_3h, y_n + h(a_{31}k_1 + a_{32}k_2 + a_{33}k_3 + a_{34}k_4 + a_{35}k_5 + a_{36}k_6))
$$


$$
k_3 = f(t_n + \frac{2h}{5}, y_n + h(\frac{4k_1}{25} + \frac{6k_2}{25}))
$$


$$
k_4 = f(t_n + C_4h, y_n + h(a_{41}k_1 + a_{42}k_2 + a_{43}k_3 + a_{44}k_4 + a_{45}k_5 + a_{46}k_6))
$$


$$
k_4 = f(t_n + h, y_n + h(\frac{k_1}{4} - 3k_2 + \frac{15k_3}{4}))
$$


$$
k_5 = f(t_n + C_5h, y_n + h(a_{51}k_1 + a_{52}k_2 + a_{53}k_3 + a_{54}k_4 + a_{55}k_5 + a_{56}k_6))
$$


$$
k_5 = f(t_n + h\frac{2h}{3}, y_n + h(\frac{2k_1}{27} + \frac{10k_2}{9} - \frac{50k_3}{81} + \frac{8k_4}{81}))
$$


$$
k_6 = f(t_n + C_6h, y_n + h(a_{61}k_1 + a_{62}k_2 + a_{63}k_3 + a_{64}k_4 + a_{65}k_5 + a_{66}k_6))
$$


$$
k_6 = f(t_n + h\frac{4h}{5}, y_n + h(\frac{2k_1}{25} + \frac{12k_2}{25} - \frac{2k_3}{15} + \frac{8k_4}{75}))
$$


La función Rungue-Kutta es la siguiente:

```Julia
function RK5(f, h, t, y_n, u)
   k_1 = f(t, y_n, u)
   k_2 = f(t + h/3, y_n + (h*k_1)/3, u)
   k_3 = f(t + 2*h/5, y_n + h*(4*k_1/25+6*k_2/25), u)
   k_4 = f(t + h, y_n + h*(k_1/4 - 3*k_2 + 15*k_3/4), u)
   k_5 = f(t + 2*h/3, y_n + h*(2*k_1/27 + 10k_2/9 - 50*k_3/81 + 8*k_4/81), u)
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
u(t) = k_p e(t) + k_i \int e(t) dt + k_d \frac{de(t)}{dt} 
$$


```Julia
function Control_PID(x, e, ie, de)
   k_p = x[1]
   k_i = x[2]
   k_d = x[3]
   u = k_p*e + k_i*ie + k_d*de
   return u
end

```





