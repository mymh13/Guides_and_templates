# Tidskomplexitet och Big-O

## Vad är Big-O och varför används det?
Big-O används för att beskriva hur mycket tid (eller minne) en algoritm behöver i förhållande till inputens storlek n. Det ger en övergripande idé om algoritmens effektivitet, oavsett vilken dator eller specifika implementation du använder.  
1.	n: Det är storleken på den data som algoritmen behandlar. Exempel:  
-	Om du letar efter ett värde i en lista med 100 element, är n=100
-	Om listan växer till 1 000 element, är n=1000

2.	Operationer: Detta är antalet gånger en specifik åtgärd i koden körs, som att jämföra värden, lägga till i en lista, eller liknande.  

3.	Big-O: Beskriver hur operationerna växer i förhållande till n.  
  
## De vanligaste komplexiteterna
### Funktionerna nedan beskriver hur antalet operationer växer när n ökar:
1.	O(1): Konstant tid.  
-	Algoritmen kör lika många operationer oavsett storleken på n. (tar alltid samma tid oavsett n)  
-	Exempel: Kontrollera om en lista är tom.
```cs
int GetFirstElement(int[] array)
{
    return array[0]; // En enda operation, oberoende av arrayens storlek
}
```

2.	O(log n): Logaritmisk tid.
-	Antalet operationer växer långsamt när n blir större.
-	Exempel: Binär sökning i en sorterad lista.
```cs
int BinarySearch(int[] sortedArray, int target)
{
    int left = 0, right = sortedArray.Length - 1;
    while (left <= right)
    {
        int mid = (left + right) / 2;
        if (sortedArray[mid] == target)
            return mid;
        else if (sortedArray[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    return -1; // Target finns inte
}
```

3.	O(n): Linjär tid.
-	Antalet operationer växer proportionellt med n.
-	Exempel: Gå igenom en lista element för element. (iterera genom en lista)  
```cs
int FindSum(int[] array)
{
    int sum = 0;
    foreach (int num in array)
    {
        sum += num; // En operation per element
    }
    return sum;
}
```

4.	O(n log n): Kombinerad.
-	Vanligt för algoritmer som sortering (ex. Quicksort).
```cs
void QuickSort(int[] array, int left, int right)
{
    if (left < right)
    {
        int pivot = Partition(array, left, right);
        QuickSort(array, left, pivot - 1);
        QuickSort(array, pivot + 1, right);
    }
}

int Partition(int[] array, int left, int right)
{
    int pivot = array[right];
    int i = left - 1;
    for (int j = left; j < right; j++)
    {
        if (array[j] <= pivot)
        {
            i++;
            (array[i], array[j]) = (array[j], array[i]);
        }
    }
    (array[i + 1], array[right]) = (array[right], array[i + 1]);
    return i + 1;
}
```

5.	O(n2): Kvadratisk tid.
-	Operationer ökar kraftigt med n, som n × n.
-	Exempel: Dubbel loop för att jämföra alla par element i en lista.
```cs
void PrintAllPairs(int[] array)
{
    for (int i = 0; i < array.Length; i++)
    {
        for (int j = 0; j < array.Length; j++)
        {
            Console.WriteLine($"({array[i]}, {array[j]})");
        }
    }
}
```

6.	O(2n): Exponentiell tid.
-	Antalet operationer fördubblas med varje ökning av n.
-	Exempel: Lösning av vissa rekursiva problem. (ex: Fibonacci-beräkning med rekursion)  
```cs
int Fibonacci(int n)
{
    if (n <= 1)
        return n;
    return Fibonacci(n - 1) + Fibonacci(n - 2); // Dubbla rekursioner
}
```

7.	O(n!): Faktoriell tid.
-	Antalet operationer växer extremt snabbt.
-	Exempel: Beräkna alla möjliga arrangemang av n objekt.
```cs
void Permute(int[] array, int start = 0)
{
    if (start == array.Length - 1)
    {
        Console.WriteLine(string.Join(",", array));
        return;
    }
    for (int i = start; i < array.Length; i++)
    {
        (array[start], array[i]) = (array[i], array[start]);
        Permute(array, start + 1);
        (array[start], array[i]) = (array[i], array[start]);
    }
}
```

## Sammanfattning:
O beskriver algoritmens växttakt, medan n är inputens storlek.  
Ju högre Big-O-komplexitet, desto mer ineffektiv är algoritmen när n växer.  
Visualiseringen med grafer hjälper dig att jämföra hur olika algoritmer presterar med ökande input.  

# Ring buffer (circular buffer)

### Ringbuffert eller cirkulär buffert på barbar-språk

En ringbuffert (eller cirkulär buffert) är en datastruktur med fast storlek som "wrappar runt" sig själv när den når slutet. Den används ofta för att buffra dataflöden eller implementera köer där storleken är begränsad.

```cs
public class RingBuffer<T>
{
    private readonly T[] buffer;
    private int head; // Points to the next write position
    private int tail; // Points to the next read position
    private int size; // Tracks the number of elements currently in the buffer

    public RingBuffer(int capacity)
    {
        buffer = new T[capacity];
        head = 0;
        tail = 0;
        size = 0;
    }

    // Add an item to the buffer
    public void Write(T item)
    {
        if (IsFull())
        {
            throw new InvalidOperationException("Buffer is full!");
        }
        buffer[head] = item;
        head = (head + 1) % buffer.Length; // Wrap around
        size++;
    }

    // Remove an item from the buffer
    public T Read()
    {
        if (IsEmpty())
        {
            throw new InvalidOperationException("Buffer is empty!");
        }
        T item = buffer[tail];
        tail = (tail + 1) % buffer.Length; // Wrap around
        size--;
        return item;
    }

    public bool IsEmpty() => size == 0;

    public bool IsFull() => size == buffer.Length;

    public int Capacity => buffer.Length;

    public int Count => size;
}
```

Exempel på den i action:  

```cs
class Program
{
    static void Main()
    {
        var ringBuffer = new RingBuffer<int>(5);

        // Write some data
        ringBuffer.Write(1);
        ringBuffer.Write(2);
        ringBuffer.Write(3);

        // Read and print
        Console.WriteLine(ringBuffer.Read()); // Output: 1
        Console.WriteLine(ringBuffer.Read()); // Output: 2

        // Write more data
        ringBuffer.Write(4);
        ringBuffer.Write(5);
        ringBuffer.Write(6);

        // Read the remaining data
        while (!ringBuffer.IsEmpty())
        {
            Console.WriteLine(ringBuffer.Read());
        }

        // Output:
        // 3
        // 4
        // 5
        // 6
    }
}
```

### Funktioner i en ringbuffert
1. Skriva (Write): Lägger till ett element vid den position som pekas ut av head. Om head når slutet av bufferten, börjar den om från början (wrappar runt).
2. Läsa (Read): Tar bort och returnerar ett element från den position som pekas ut av tail. Även här wrappar den runt om den når slutet.
3. Kapacitet: Bufferten har en fast storlek. När bufferten är full kan inga nya element läggas till förrän något har lästs bort.
4. Effektivitet: Både skriv- och läsoperationer är 𝑂(1), vilket gör ringbufferten mycket effektiv.  

### Användningsområden

- Dataflöden: Buffring av ljud, video eller andra typer av strömmar där datan fylls och läses kontinuerligt.
- Producer-Consumer-mönster: I system där en tråd producerar data och en annan tråd konsumerar den.
- Resursbegränsning: För att hantera köer eller lagring där en fast storlek behövs för att spara minne.  

### Typiska problem som hanteras
- Wrappar runt: Ringbufferten hanterar att positionerna för skrivning och läsning återgår till början av bufferten automatiskt.
- Överfyllning: Kontrollera om bufferten är full för att undvika att skriva över osparad data.
- Undertömning: Kontrollera om bufferten är tom för att undvika att läsa från en plats utan data.

### Sammanfattning
En ringbuffert är en praktisk och effektiv datastruktur för att hantera kontinuerliga dataflöden eller begränsade resurser, tack vare dess enkla implementation och snabba operationer.

