Download Link: https://assignmentchef.com/product/solved-csi2120-lab-2
<br>
<h3>Exercise 1: Basic Methods</h3>

Define the type <tt>dog</tt> as follows:

<pre><code>type dog struct {  name string  race string  female bool}</code></pre>

Write a method that will rename a dog which can be used as follows:




<pre><code>func main() {    fido := dog {"Fido", "Poodle", false }    fido.rename("Cocotte")}</code></pre>

<h3>Exercise 2: Go Channel Basics</h3>

Test what will be printed by the following

<pre><code>package mainimport (    "fmt"    "time"    "strconv")var i intfunc makeCakeAndSend(cs chan string) {    i = i + 1    cakeName := "Strawberry Cake " + strconv.Itoa(i)    fmt.Println("Making a cake and sending ...", cakeName)    cs &lt;- cakeName //send a strawberry cake}func receiveCakeAndPack(cs chan string) {    s := &lt;-cs //get whatever cake is on the channel    fmt.Println("Packing received cake: ", s)}func main() {    cs := make(chan string)    for i := 0; i&lt;3; i++ {        go makeCakeAndSend(cs)        go receiveCakeAndPack(cs)        //sleep for a while so that the program doesn't exit        //immediately and output is clear for illustration        time.Sleep(1 * 1e9)    }} </code></pre>

<h3>Exercise 3:</h3>

The following program is to be changed such that it uses arbitrary sized slices instead of fixed size arrays. In particular, the user entered size (variable sz) is to replace the hard-coded 32 in main and any function changed correspondingly.

<pre><code>package mainimport "fmt"import "math"import "math/rand"type Series struct {	a, b float64}func (s Series) add(t, TP int) float64 {	return s.a*math.Sin(2.0*math.Pi*float64(t)/float64(TP)) + s.b*math.Cos(2.0*math.Pi*float64(t)/float64(TP))}func fourier(c [32]Series, t, TP int) (res float64) {	res = c[0].a	for n := 1; n &lt; 32; n++ {		res += c[n].add(t, TP)	}	return}func main() {	TP := 4	sz := 1	var res float64	// Enter size of fourier series	fmt.Print("Size of series (1 ... 512): ")	// Depending on your environment you may have to remove 
 in the scanf	_, err := fmt.Scanf("%d 
", &amp;sz)	for err != nil || sz &lt; 1 || sz &gt; 512 {		fmt.Println("Must be positive integer (1...512).")		fmt.Println("Size of series (1 ... 512): ")		_, err = fmt.Scanf("%d 
", &amp;sz)	}	fmt.Printf("Size: %d
", sz)	var c [32]Series	for t := 0; t &lt; TP; t++ {		for k := 0; k &lt; 32; k++ {			c[k].a = rand.Float64()			c[k].b = rand.Float64()		}		res += fourier(c, t, TP)		fmt.Printf("%f ", res)		fmt.Println()	}}</code></pre>

<h3>Exercise 4 and Quiz: Semaphore</h3>

<em>Please hand-in the answer to this question as a single go file on Virtual Campus during your lab session but at the latest by Friday 6:00pm! Remember, your submission will only count if you have signed the lab attendance sheet.</em>

While go has laguage level support for CSP style concurrency, a resource guided by a semaphore can be easily implemented. The program below uses two go routines to access a variable, one routine counting up and one counting down. Each runs a loop and accesses the variable the same number of times. Naively, the variable should be the same as in the beginning. This is not the case. Use a global semaphore (the variable <code>sema</code> below) to signal the use of the variable <code>Count</code> by the functions <code>increment</code> and <code>decrement</code>.

<pre><code>package mainimport (	"fmt"	"time")var (	Count      int = 0	nFunctions int = 2)var sema = make(chan int)func main() {	ch := make(chan int)	fmt.Printf("Count = %d
", Count)	go increment(ch, 1000)	go decrement(ch, 1000)	for i := 0; i &lt; nFunctions*1000; i++ {		fmt.Printf("Waiting %d
", Count )		&lt;-ch	}	fmt.Printf("Count = %d
", Count)}func increment(ch chan int, nSteps int) {	for i := 0; i &lt; nSteps; i++ {		cnt := Count		time.Sleep(3 * time.Millisecond)		Count = cnt + 1		ch &lt;- 1	}	return}func decrement(ch chan int, nSteps int) {	for i := 0; i &lt; nSteps; i++ {		cnt := Count		time.Sleep(2 * time.Millisecond)		Count = cnt - 1		ch &lt;- 1	}	return}</code></pre>