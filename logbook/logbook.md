# Communications - Lab Logbook

## Lab 1 - Introduction to LabView

### Exercise 1 - Data Types in LabView

This exercise involved converting between some of the basic data types in LabView. Specifically, we converted each letter of a string into its ASCII binary representation, which was shown in its equivalent unsigned integer form. We then treated each integer as a Fahrenheit temperature, converted it to Celcius, took an average and compared to a threshold value. We finally indicated if the string inputted was considered "Hot".

First, we opened LabView, created a new project and a new VI `DataTypes.gvi`.

In the **Diagram tab** of the main window, we added the **String to Byte Array** module from the function palette. We then added a control as an input and an indicator at the output.

Adding these items in the panel tab, we can view the byte array in the form of unsigned integers representing the ASCII values of each character of the string we input. For example, when we input the string `Hello`, we got the following output:

<img src="images/lab1/task1hello.PNG">

This is because the ASCII symbol `H` corresponds to ASCII code `72` in decimal, `e` corresponds to ASCII code `101` in decimal, `l` corresponds to ASCII code `108` and so on.

We then considered the decimal ASCII code to be a temperature in Fahrenheit. We then converted this to a temperature in Celcius by using the following formula:

$$\degree \text{C} = (\degree \text{F} - 32) \div 1.8 $$

We therefore added a subtraction and division block to our diagram. 

Next we wanted to calculate the average of the array of converted temperatures. We added the **Add Array Elements** (essentially sums all the non-zero elements of the array up) and the **Array Size** blocks to our diagram as well as another diviison block. We then connected the output of the **Add Array Elements** block to the dividend input of the division block and the output of the **Array Size** to the divisor input of the division block. We also created an output indicator showing the average temperature.

Finally, we applied a threshold using the **Greater or Equal** module, setting the constant to $50$ and comparing the average value to this constant. We then added a boolean indicator `Hot?` as the output of the **Greater or Equal** module.

<img src="images/lab1/task1block.PNG">

In summary: we input a string, receieve an array of ASCII codes corresponding to each character of the string, we then consider these integers to be temperatures in Fahrenheit and convert these values to Celcius, find the average and then compare with $50$. If the average temperature in Celcius is greater than or equal to $50$, the boolean indicator `Hot?` is true (lights up blue in the panel).

We then tested this by inputting both our surnames in the `String Constant` field:

<img src="images/lab1/task1Lewis.PNG">

<img src="images/lab1/task1Liu.PNG">

Rearranging the above equation for $\degree \text{F}$, we get:

$$ \degree \text{F} = (\degree \text{C} \times 1.8) + 32 $$

And since our threshold is $50$, we set $\degree \text{C} = 50$ and so the minimum ASCII character code to exceed the threshold is $122$ which corresponds to the ASCII symbol `z`:

<img src="images/lab1/task1zzz.PNG">

And hence the indicator `Hot?` is True and lights up blue in the panel.

---

### Exercise 2 - Implementation of the Central Limit Theorem (CLT)

In this exercise we visualise the CLT. We generate independent, uniformly distributed random numbers, and the convergence of the sample mean value to a normal distribution will be observed as the number of 
trials increases. 

First, we created a new VI called `CLT.gvi`. What we want to do is to generate a vector of iid random numbers (which we will do with a for loop), evalulate their sample mean and iterate this whole process within a while loop. After each iteration of the while loop, we wlil update the histogram of the mean values to observer hte convergence of the distribution.

First, we added a **While Loop** in the diagram. This contains an iteration output which tells us the current iteration in the loop, as well as as conditional terminal which allows us to terminate the loop if a certain condition is met. Initially, we just connected a **Stop** control input to this. Within the while loop, we also added the **Wait** module which acts as a delay between each iteration of the loop.

Within the while loop, we then added 2 nested for loops, connecting constants to both loop count terminal of each. This defines how large our 2D array will be. Initially we set this to $5$ for both.

Within the for loops, we placed a **Random Number** generator block, and connected its output to an **Add Array Elements** block. As data is transferred from within a loop to outside a loop, it does so via a tunnel. In our case, we used an **auto-indexing** output tunnel which appends the values obtained from a single loop iteration to an increasing array of data.

We then calculated the mean value by dividing the output of the **Add Array Elements** block by the total number of elements in the array, which in our case was $5 \times 5 = 25$.

Next, we created an array of these sample means to obtain a histogram. We placed an **Insert into Array** function on the block diagram outside the for loops but within the while loop which inserts each sample mean into a specific position in the array. In our case, we used the iteration of the while loop to index into this block.

We also needed to add a base array to add all these new elements to, and it needs to be available 
at each iteration. To do this, we use a shift 
register which is a type of tunnel that carries information from one iteration to the next. First, we
connected the output array of the **Insert into Array** function to the right frame of the While loop. This creates a tunnel by default.

We then right clicked on the tunnel and select **"Change with Shift Register"**. This created another tunnel on the left frame of the loop. We then connected this new tunnel to the **"array"** terminal of the **Insert into Array** function. The tunnel on the left edge carries the data from the previous 
iteration, whereas the one on the right edge sends the data to the next iteration.

Finally, for the **"input"** terminal of the left register, we created a constant array so it can initialise the program with an array of 0s. We then also added the histogram block with an output indicator and a control input for **"number of bins"**. 

The **number of bins** refers to the number of intervals into which the data range is divided. Each bin represents a specific range of values and the height of the bin indicates how many data points fall within that range. For example, if we have data ranging from $0$ to $100$, if we have $4$ bins, each bin might cover: $0$-$25$, $25$-$50$, $50$-$75$ and $75$-$100$.

This was our final block diagram:

<img src="images/lab1/task2block.png">

We then ran the program, setting wait time to $10$ ms and number of bins to $100$. We let the program run for roughly $5000$ simulations before stopping it and this was the resulting histogram:

<img src="images/lab1/task2_5000iterations.PNG">

We then created a control for stopping the execution of the program after 1000 iterations. This just involved adding an **Equal** block and comparing the iteration number to a constant 1000:

<img src="images/lab1/task2block_stop1000iterations.PNG">

We then ran the program again, this time setting the number of bins to 10. Notice that the histogram is more generalised and appears more smooth as the data range of each interval is greater so we see less detail. This time, the count automatically stopped at 1000.

<img src="images/lab1/task2_1000iterations.PNG">

Finally, we revised the code such that the final distribution we see is a standard normal distribution (zero mean and unit variance). We know that the random number generator follows a **continuous uniform distribution** $U(0,1)$, meaning all values in that range are equally likely to be generated.

We know that the mean $\mu$ of a continuous uniform distribution is: 

$$\mu=\dfrac{\alpha + \beta}{2}$$

where in our case, $\alpha = 0$ and $\beta = 1$ and thus $\mu = 0.5$.

We also know that the variance of a continuous uniform distribution is:

$$\sigma ^ 2=\dfrac{(\beta - \alpha)^2}{12}$$

which means $\sigma=\dfrac{1}{\sqrt{12}}$.

Finally, we know that if $X \sim N(\mu , \sigma ^2)$, then the standard normal distribution is given by $Z=\dfrac{X-\mu}{\sigma} \sim N(0,1)$.

Therefore we have to subtract $\mu = 0.5$ from each value and then divide this by $\sigma = \dfrac{1}{\sqrt{12}}$.

This can be done easily by just adding an additional subtract block, two division blocks and a square root block:

<img src="images/lab1/task2block_standardnormal.PNG">

We then set the number of bins to 20 which seemed like a good balance between a smooth distribution whilst also maintaining a reasonable level of detail and we got the following distribution:

<img src="images/lab1/task2_standardnormal.PNG">

Clearly, we can see that the distribution is centered around 0 (i.e. $\mu = 0$) and the variance appears to be roughly 1 as it looks like around 66% of the data is within 0.5 either side of the mean.

We also ran a final test with 20,000 iterations and 50 bins, and we observed a distribution that was very similar to a standard normal distribution:

<img src="images/lab1/task2_20kiterations.PNG">

---

### Exercise 3 - AM Modulator

In this exercise, we built an AM Modulator. We know that the equation for modulating a single tone (single-frequency signal) is given by:

$$ s(t)=[A_c + A_m \cos(2 \pi f_m t)]\cos(2 \pi f_c t) $$

where $A_m$ and $A_c$ are the amplitudes, and $f_m$ and $f_c$ are the frequencies of the message and carrier waveforms respectively.

First, we created a new VI called `AM.gvi` and using the **Wave Generator** module, we created two **Sine waveforms** corresponding to the message and carrier waveforms. We created controls for frequency, amplitude, sample rate and samples for each waveform.

We then assigned values to the terminals of the waveform generators. We set the sample rate of both the carrier and message signal to $200 \text{k}$ which is the number of samples generated per second. A higher sample rate improves the resolution but requires more processing power. We also set the samples to $1000$ which means there are $1000$ discrete points that make up the waveform. So, with a $200 \text{kHz}$ sample rate and $1000$ samples, we generate a waveform lasting $5 \text{ms}$.

$t_0$ (which is the starting time of the waveform) was also set to epoch which refers to time 0 in a given reference system which is useful for synchronising waveforms across systems. In our case, epoch was 01/01/1904, 00:00.

We then added the **FFT Power Spectrum and PSD** module to the diagram. This module analyses the frequency content of a signal using the FFT. The Power Spectrum computes the magnitude squared of the FFT, representing the power present at each frequency component:

$$ P(f)=|X(f)|^2 $$

Switching the module to **PSD**, the power spectrum is simply normalised by the frequency resolution $\Delta f$, i.e. the **frequency bin width**:

$$ \text{PSD}(f)=\dfrac{|X(f)^2|}{\Delta f} $$

Changing to PSD is useful as it gives a density which is useful when comparing power between different signals.

Finally, we performed some multiplication, division and addition operations to obtain the AM waveform. From AM theory:

$$ s(t)= [A_c + A_m \cos(2 \pi f_m t)]\cos(t \pi f_c t) $$

which can be rewritten as:

$$ s(t) = \frac{1}{A_c}[A_m\cos(2 \pi f_m t)][A_c\cos(2 \pi f_c t)]+ A_c\cos(2 \pi f_c t) $$

and so we therefore had a **Multiply (Waveform)** module which multiplied the message and carrier waveforms, a **Divide (Waveform)** module to divide by $A_c to remove the extra scaling factor $A_c$ and finally we used an **Add (Waveform)** module to add the carrier component to the result. We then created an indicator to show the AM waveform and PSD. We also created a Sub-VI for the AM module.

<img src="images/lab1/task3block.PNG">

We also know that the modulation index, $\mu$ is defined as $\mu=\frac{A_m}{A_c}$. The modulation index can be changed by adjusting $A_c$ and $A_m$ in the panel and this defines how much the carrier amplitude varies according to the message amplitude.

First, we set $A_c=2$ in the panel, and changed $A_m$ to obtain modulation index values $\mu=\{0.5, 1, 1.5\}$ which corresponded to $A_m=\{1, 2, 3\}$ respectively. These were the plots of the time domain and PSD representation of the message, carrier and modulated signals for each value of $\mu$.

$\mu=0.5$:
<img src="images/lab1/task3plots_0.5mu.PNG">

$\mu=1$:
<img src="images/lab1/task3plots_1mu.PNG">

$\mu=1.5$:
<img src="images/lab1/task3plots_1.5mu.PNG">


We see that for all the modulation indexes above, the envelope of the AM waveform is generated by the message signal. For $\mu=0.5$, the carrier amplitude varies by 50% above and below the unmodulated level (which is 2, hence why the amplitude varies between 1 and 3), and as such the amplitude of the AM signal never reaches 0. In short, the amplitude variation is smaller than the carrier's original amplitude. This is a case of an under-modulated signal ($\mu < 1$).

For $\mu=1$, the carrier amplitude varies by 100% above and below the unmodulated level, i.e. the amplitude varies exactly between 0 and twice the carrier amplitude which is 4. This is critical modulation and this yields the highest SNR.

For $\mu=1.5$, the envelope crosses 0 and inverts, leading to envelope distortion. This signal is over-modulated ($\mu > 1$). In this case, a receiver may struggle to properly demodulate the signal as it cannot simply follow the envelope of the message.

To summarise, as $\mu$ increase, the depth of the amplitude variation increases.

We also see from the PSD of the AM signals that there are three main components visible: the **carrier frequency** $f_c$ azt the centre, the **upper sideband (USB)** at $f_c+f_m$ and the **lower sideband (LSB)** at $f_c-f_m$, and it is the sidebands that contain the actual message information.

For $\mu=0.5$ (under-modulated), the sidebands are present but have low energy. This is because the carrier remains dominant meaning most of the power is in the carrier rather than the message (inefficient power usage).

For $\mu=1$ (critical modulation), the sidebands have equal power. We know that the total transmitted poewr in AM is:

$$ P_{total}=P_c+P_{USB}+P_{LSB} $$

and at critical modulation, the power in the sidebands together equals 50% of the total power, i.e. the total sideband power is equal to the carrier power:

$$ P_{USB}=P_{LSB}=\dfrac{P_c}{2} $$

$$ P_{USB}+P_{LSB}=P_c=\dfrac{P_{total}}{2} $$

For $\mu=1.5$ (over-modulated), the sidebands have more energy but nonlinear distortion occurs, introducing frequency components (harmonics). 

Finally, we set $A_c=A_m=1$ ($\mu=1$) and changed the frequency of the message signal $f_m=\{1\text{k}, 2\text{k}, 5\text{k}\}$ and these were the plots we obtained at each frequency:

$f_m=1\text{kHz}$:
<img src="images/lab1/task3plots_1kf.PNG">

$f_m=2\text{kHz}$:
<img src="images/lab1/task3plots_2kf.PNG">

$f_m=5\text{kHz}$:
<img src="images/lab1/task3plots_5kf.PNG">

We can clearly see from these plots that as the message frequency $ f_m $ increases, the envelope oscillates faster. We also see that the time domain waveforms at $2\text{kHz}$ and especially $5\text{kHz}$ have significant distortion. We also see the sidebands move further away from the central carrier frequency, which increases the required bandwidth ($ B=2f_m $).

The distortion is likely due to aliasing/sampling issues where our sample rate is not high enough. In a real system, there may also be a bandwidth limitation that would affect this.

---

## Lab 4 - Binary Phase Shift Keying (BPSK) via USRP

In phase-shift keying (PSK) modulation, information is encoded on the phase of the transmitted carrier rather than on its amplitude (ASK) or frequency (FSK).

In binary phase-shift keying (BPSK), there are two phase values: $0 \degree$ or $180 \degree$; i.e. an unmodified carrier is transmitted to represent one binary data value, whilst an inverted carrier is transmitted to represent the other. BPSK is optimum among binary modulation schemes in achieving the lowest average power for a given target bit error ratio (BER).

---

### Exercise 1 - BPSK Transmitter

The BPSK signal is given by the equation:

$$ A g_{TX}(t)\cos(2\pi f_c t + \theta (t)) $$

where $A$ is a constant corresponding to the transmitted power level, $g_{TX}(t)$ is a fixed pulse shape, $f_c$ is the carrier frequency and $\theta(t)$ takes the value of either $0\degree$ or $180\degree$ to carry the desired information. Therefore, the equation can be rewritten as:

$$ \pm A g_{TX}(t)\cos(2\pi f_c t)$$

since $\cos(\theta + 180\degree)=-\cos(\theta)$.
We assume a new pulse is transmitted every $T$ seconds so that the symbol rate is $1/T$.

To form a BPSK signal, we first need to map the input data (a stream of bits) to symbols. The **MT Generate Bits** VI produces an array of bits from the input stream. We map the bits to the symbols in this way:

| Bit Value | Phase ($\theta$) | Symbol |
|-----------|------------------|--------|
| 0         | 0                | -1     |
| 1         | 180              | 1      |

In our case, this mapping follows the equation:

$$ s[n] = 2 b[n] - 1 $$

Also, the symbol rate and bit rate are the same in this case as each symbol represents exactly one bit (0 or 1) and since there are only two symbols (-1 and 1), these are the same.

We then do the upstampling step. This increases the sample rate before pulse shaping by placing $L-1$ zeros after each symbol.
This therefore produces a sample interval of:

$$ T_x = \dfrac{T}{L} $$

or a sample rate of 

$$ \dfrac{1}{T_x}=L \dfrac{1}{T} $$

The quantity $L$ is known as the **upsampling factor**.

Next, we do the pulse shaping step. The aims to smooth out transitions between symbols and involves passing the upsampled signal through a root-raised cosine filter. When we do this, each discrete symbol is spread out in time to form a smooth RRC pulse. The RRC pulse has a rapid spectral roll-off, which means most of the signal's energy is confined within a narrow band, which helps to limit the bandwidth of the transmitted signal and reduce interference with consecutive symbols (avoid inter-symbol interference). 

We finally send the signal in the form $(\pm 1+0j)g_{TX}[n]$ directly to the USRP. The USRP can then do the digital-to-analogue conversion and upconversion (multiplying by the carrier $\cos(2 \pi f_c t)$) which are necessary steps to convert the digital baseband signal into a real analogue RF signal.

First, we added the **MT Generate Bits** module which creates a pseudorandom sequence of bits fo tthe BPSK system. We added a control to specify the total number of bits.

We then performed the symbol mapping, implementing this basic equation:

$$ s[n] = 2 b[n] - 1 $$

 We then include the **AddFrameHeader** sub-VI after this. This module inserts a specific 26-bit sequence at the start of the transmission, which is used to align the recieved bits. This is needed because the receiver doesn't inherently know where the first bit of the message start. This 26-bit header is then found at the receiver and is cut off (along with any bits before the frame header) which were just noise or misaligned data.

We then upsample the array of symbols using the **Upsample** module. We use the symbol rate $\frac{1}{T}=10,000$ (which is the symbols per second) and the IQ rate $\frac{1}{T_x}=200,000$ (which is the samples per second) to calculate the upsampling factor:

$$L=\dfrac{\text{IQ Rate}}{\text{Symbol Rate}}=\dfrac{\frac{1}{T_x}}{\frac{1}{T}}=\dfrac{T}{T_x}=\dfrac{200,000}{10,000}=20$$

This means each symbol is represented by 20 samples.

We then added a **MT Generate Filter Coefficients** module to generate the pulse-shaping filter and this is convolved using the **Convolution** module to to apply this filter to the unsampled symbols.

We then normalised the amplitude of the filtered message to a maximum absolute value of 1, presumably to prevent clipping or distortion. Next, we added the **Build Waveform** function and connected the normalised signal as in input to this. 

We also set the $dt$ value (which is the sampling time interval) to $\frac{1}{\text{IQ Rate}}=\frac{1}{200,000}=5\mu \text{s}$. We then placed an indicator at the output to observe the BPSK-modulated baseband waveform.

We then convert the the output of the normalised signal to complex values as this is what the USRP requires for transmission. Of course in BPSK, the imaginary part is 0, but this still needs to be formatted in this way.

We also wanted to observe the baseband power spectrum. But first, we had to connect our normalised signal to a **Cluster Properties** module (this organises and formats the data points to prepare the signal for FFT processing by ensuring it has the correct data type (Complex Double: CDB), the correct $dt$ time spacing and other formatting). 

We also added the **Insert Pilots** sub-VI which inserts a specific sequence of symbols, which helps the receiver estimate and correct channel distortions. We then add another frame header (complex, this time) to mark the start of transmission. We finally connect to **niUSRP Write Tx Data** which sends the complex BPSK signal to the USRP hardware for actual transmission.

This was our completed block diagram:
<img src="images/lab4/task1/BPSK_Tx_block.PNG">

In the panel, we then assigned these values as shown:
<img src="images/lab4/task1/values.PNG">

Clearly, given the symbol rate $\frac{1}{T}=10,000$ and the IQ rate $\frac{1}{T_x}=200,000$, the number of samples per symbol, or the upsampling factor 

$$L=\dfrac{\text{IQ Rate}}{\text{Symbol Rate}}=\dfrac{200,000}{10,000}=20$$

These were the plots we obtained.

Baseband Waveform:
<img src="images/lab4/task1/baseband_waveform.PNG">

Baseband Power Spectrum:
<img src="images/lab4/task1/baseband_power_spectrum.PNG">

From the baseband waveform plot, we can see that the waveform resemble pulses, either +1 or -1. Also, given that we used root-raised consine pulse shaping, we have smooth transitions between pulses, avoiding any sharp edges. From the power spectrum, we see that the spectral roll-off is very sharp since we are using root-raised cosine filtering. This means the majority of the energy is spread within the main lobe.

Zooming in on the main lobe, we can measure the main lobe bandwidth:

<img src="images/lab4/task1/baseband_power_spectrum_main_lobe.PNG">

We see that the main lobe has a bandwidth of approximately $14\text{kHz}$.

We then changed the pulse shaping filter control to 'None' to create rectangular pulses and we observed these plots:

Baseband Waveform:
<img src="images/lab4/task1/baseband_waveform_rectangular.PNG">

Baseband Power Spectrum:
<img src="images/lab4/task1/baseband_power_spectrum_rectangular.PNG">

Looking at the baseband waveform, we now get perfect rectangular pulses at +1 or -1. From the power spectrum, we see that the spectral roll-off compared to the root-raised cosine is much more gradual and much less sharp.

Zooming in as before, we can measure the bandwidth of the main lobe:

<img src="images/lab4/task1/baseband_power_spectrum_main_lobe_rectangular.PNG">

And the bandwidth of the main lobe is now $20\text{kHz}$ which is significantly more than with the root-raised cosine.

The root-raised cosine is designed to minimise inter-symbol interference (ISI) and hence has a sharp spectral roll-off and a narrower bandwidth.

---

### Exercise 2 - BPSK Receiver

The BPSK signal that arrives at the receiver is given by:

$$ r(t)=\pm Dg_{TX}(t) \cos(2\pi f_c t + \varphi) $$

where $D$ is a constant (usually smaller than A, as the received signal will be attenuated) and $\varphi$ represents the difference in phase between the transmitter and receiver carrier oscillators. 

Assuming the receiver's carrier oscillator is set to the same frequency as the transmitter, then the USRP receiver does the majority of the work in demodulating the BPSK signal. It is demodulated by multiplying it with the receiver's carrier oscillator signal $\cos(2 \pi f_c t)$:

$$ r_{demod}(t)=r(t)\cdot \cos(2\pi f_c t)=Dg_{TX}(t) \cos(2\pi f_c t + \varphi)\cos(2\pi f_c t)$$

$$ r_{demod}(t)=Dg_{TX}(t)\cdot \dfrac{1}{2}[\cos(4\pi f_c t + \varphi) + \cos(\varphi)]$$

where we have used trigonometric identities to simplify.

The term $\cos(4 \pi f_c t + \varphi)$ is a high-frequency component which would be filtered out by a LP filter, giving:

$$ r_{baseband}(t)=\dfrac{D}{2}g_{TX}(t)\cos(\varphi)=\dfrac{D}{2}g_{TX}(t)e^{j\varphi}$$

It is then sampled at intervals $T$ and the $\pm$ comes from the BPSK modulation: $+\frac{D}{2}$ for symbol "$1$" and $-\frac{D}{2}$ for "$-1$".
 
Therefore the receiver's **Fetch Rx Data** function provides this sequence of output samples, given by:

$$ \tilde{r}[n]=\pm \frac{D}{2}g_{TX}[n]e^{j\varphi} $$

Similar to the transmitter, the sampling rate $\frac{1}{T_z}$ is set by the receiver's **IQ Rate** and this rate is set so as to provide $M$ samples every $T$ seconds, where $\frac{1}{T}$ is the sample rate.

To obtain the transmitted signal, we first need to do **Channel Estimation** to remove the phase ambiguity caused by the unsynchronised transmitter (Tx) and receiver (Rx) oscillators. It works by reading the received pilots (known symbols) which we inserted in the Tx signal, and comparing these with the expected ones to estimate the channel transfer function using a least-squares estimation. The inverse of the transfer function is then applied to the Rx signal to try to remove the phase offset.

We then perform matched filtering, which serves to maximise the SNR and reduce the inter-symbol interference (ISI). We use a root-raised cosine filter, and the impulse response $g_{RX}[n]$ is matched to the pulse shape $g_{TX}[n]$ of the transmitted signal.

The matched filte routput is an analog baseband signal that needs to be sampled once every $T$ seconds, i.e. once per symbol time. Due to propagation delays, the filtering and channel distortion, we have to determine the optimal sampling instants to take the samples and this is achieved by the **PulseAlign** sub-VI.

The **Decimate** module then samples the aligned baseband waveform at index 0 and every $T$ seconds thereafter. This is the process of **downsampling** the baseband signal to extract one sample per symbol period $T$. The input to the Decimate function is a high-rate signal (i.e. $M \times$ the symbol rate, where $M$ is the matched samples per symbol or the decimation rate). For example, if our input samples are $x[0], x[1], x[2], x[3], x[4], x[5], ...$ and we have a decimation factor $M=4$, out output samples will be $x[0], x[4], x[8], ...$.

Finally, once we have sampled the baseband waveform, each sample must be examined to determine whether it represents the symbol $1$ or $-1$. These detected symbols are then converted to bits by carrying out the opposite of the calculation we did in the transmitter:

$$ b[n] = \dfrac{s[n] + 1}{2}$$

Alternatively, a **Greater than 0?** function also works.

To actually implement this, we first added a **FrameSync (Complex)** module. This synchronises the receiver to the transmitted signal by detecting the header (26-bit sequence) that was added in the AddFrameHeader sub-VI in the transmitter. It then discards the header (along with any bits before the frame header) which is just noise or misaligned data. This ensures the receiver starts processing the actual transmitted data. This module is the complex version as the received signal is complex-value (contains in-phase and quadrature copmonents, both of which are necessary to fully characterise the amplitude and phase of the signal). 

We then connected the output of this to the channel estimator which estimates the phase offset using the pilot symbols that we inserted into the Tx signal, estimates the channel transfer function and then applies the inverse of this to align the received signal to the transmitted signal. 

To implement the matched filter, we used the **MT Generate Filter Coefficients** block as in the transmitter, where the filter coefficients are for a root-raised cosine filter. We also input the matched samples per symbol, or downsampling factor $M$ to this function. This is calculated using the symbol rate $\frac{1}{T}=10,000$ (which is the symbols per second) and the IQ rate $\frac{1}{T_z}=200,000$ (which is the samples per second):

$$M=\dfrac{\text{IQ Rate}}{\text{Symbol Rate}}=\dfrac{\frac{1}{T_z}}{\frac{1}{T}}=\dfrac{T}{T_z}=\dfrac{200,000}{10,000}=20$$

Notice that this is the same value as the upsampling rate in the transmitter (obviously).

We then extract the real part of the output of the **Channel Estimator** block. We are no longer interested in the quadrature (imaginary) component as after demodulation and phase correction, the signal is primarily real-valued and the Q component is either 0 or just contains noise. This is then convolved with the filter coefficients.

To observe the baseband power spectrum, we connect the output of the convolution to a **Cluster Properties** module (this organises and formats the data points to prepare the signal for FFT processing by ensuring it has the correct data type (Complex Double: CDB), the correct $dt$ time spacing and other formatting). The $dt$ value (which is the sampling time interval) is set to $\frac{1}{\text{IQ Rate}}=\frac{1}{200,000}=5\mu \text{s}$. We then placed an indicator at the output to observe the baseband power spectrum.

We then connect the baseband output waveform (output of convolution function) and the downsampling factor $M$ to a **PulseAlign (Real)** module (since our signal is now real-valued) which aligns the signal to the optimal sampling instants. The aligned waveform can then be downsampled to one sample per symbol using the **Decimate** function, where the decimating factor is the downsampling factor $M$. Another **FrameSync (Real)** module is added to check that the sampled symbols are aligned with the transmitted data.

We then need to determine if each symbol is a 1 or a 0. This was done using a for loop. The **Greater than 0?** module maps each symbol ($-1$ or $1$) to a boolean value which can then be converted to corresponding bits ($0$ or $1$). This is then converted to an integer array of size 1000 bits (this is the length of the message).

Finally, we calculate the BER using the **MT Calculate BER** function by comparing the detected bits to the expected bits. It calculates the radio of incorrect bits to total bits. The trigger threshold is set to 0.4, which means that the receiver is considered to be synchronised if the measured BER is less than 40%. If the measured BER is above the threshold, the **trigger out?** output remains false, suggesting that the receiver is not synchronised.

This was our completed block diagram:

<img src="images/lab4/task2/task2blockfix.PNG">

And these were the values we specified in the panel:

<img src="images/lab4/task2/values.PNG">

We then ran the transmitter and receiver several times at these different gain values:

| Tx Gain(dB) | Rx Gain (dB) |
|-------------|--------------|
| 0           | 0            | 
| -35         | -15          |
| -37         | -15          |
| -40         | -15          |

The Tx gain controls the power of the transmitted signal. The lower the Tx gain, the lower the signal power making it more susceptible to noise.

The Rx gain amplifies the received signal. Lower Rx gain reduces the signal power at the receiver, increasing the impact of noise.

We are essentially trying to maximise the SNR (ratio of signal power to noise power). This higher the SNR, the lower the BER.

We would therefore expect for the high Tx and Rx gain (Tx gain $=0$ and Rx gain $=0$) to have a high SNR as both the transmitted and recieved signals are at their maximum power and so we should get a low BER.

Decreasing the Tx gain to $-35$ and the Rx gain to $-15$, the power of the transmitted signal is reduced and the received signal has a lower amplification. We therefore expect both signals to be attenuated, and so we expect the SNR to be lower than in the first case with a resulting higher BER.

Decreasing the Tx gain further we would expect a worse BER still.

The results we obtained were as follows:

| Tx Gain(dB) | Rx Gain (dB) | Reading 1 | Reading 2 | Reading 3 | Reading 4 | Reading 5 | Average BER |
|-------------|--------------|---|---|---|---|---|---|
| 0           | 0            | 0 | 0|0 |0 |0 |0 |
| -35         | -15          | 0.00737101 | 0.0221998|0.0201806 |0.00364964 | 0.00403633| 0.0114877| 
| -37         | -15          |  0.0246154| 0.0960699| 0.043573| 0.0813008| 0.0952381| 0.0681594|
| -40         | -15          |0.317511  |0.1963 |0.184694 |0.480789 |0.491264 |0.334112 |

As expected, the average BER increases as the Tx and Rx gain decrease. At Tx and Rx gain $=0$, the signal is strong enough that we get no errors at all! 

We also see that the variation in readings increases as the gain decrease, which is also expected as the system is operating closer to the threshold where communication is unreliable, where the SNR is very low.
