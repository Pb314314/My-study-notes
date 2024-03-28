# CS6290 Project1 Report

[toc]



## For gcc.trace:

First, I executed the configuration combination of (b1, s1, b2, s2, prefetch policy, replacement policy, and insertion policy) parameters for the gcc.trace.

```shell
b_values=(5 6 7)
s_values=(0 0 0 0 1 1 1 1 2 2 2 2)  
S_values=(2 3 4 5 2 3 4 5 2 3 4 5)  
insert_policy=(mip lip)
replacement_policy=(lru lfu)
prefetch_mode=(0 1 2)
trace_files=("gcc.trace")  
trace_path="./traces"
for b in "${b_values[@]}"; do
    for i in {0..11}; do
        s=${s_values[i]}
        S=${S_values[i]}
        for trace in "${trace_files[@]}"; do
            for insert in "${insert_policy[@]}"; do
                for r in "${replacement_policy[@]}"; do
                    for p in "${prefetch_mode[@]}"; do
                        ./cachesim -c 15 -b "$b" -s "$s" -C 17 -S "$S" -P "$p" -I "$insert" -r "$r" -f "$trace_path/$trace"
                        echo "Ran: -c 15 -b $b -s $s -C 17 -S $S -P $p -I $insert -r $r -f $trace_path/$trace"
                        sleep 1
                    done
                done
            done
        done
    done
done
```

I prioritized L1 Average Access Time (AAT) in analysis, sorting the results in ascending order. 

The table present the top 30 configurations with the lowest L1 AAT for the gcc trace.

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20240217212759396.png" alt="image-20240217212759396" style="zoom:33%;" />

This result shows that, the B=7 always have better L1_AAT. And I set B to 7 and test more combination of S.

```shell
b_values=(7)
s_values=(3 3 3 3 4 4 4 4 5 5 5 5)  
S_values=(3 4 5 6 4 5 6 7 5 6 7 8) 
insert_policy=(mip lip)
replacement_policy=(lru lfu)
prefetch_mode=(0 1 2)
trace_files=("gcc.trace")  
trace_path="./traces"
for b in "${b_values[@]}"; do
    for i in {0..11}; do
        s=${s_values[i]}
        S=${S_values[i]}
        for trace in "${trace_files[@]}"; do
            for insert in "${insert_policy[@]}"; do
                for r in "${replacement_policy[@]}"; do
                    for p in "${prefetch_mode[@]}"; do
                        ./cachesim -c 15 -b "$b" -s "$s" -C 17 -S "$S" -P "$p" -I "$insert" -r "$r" -f "$trace_path/$trace"
                        echo "Ran: -c 15 -b $b -s $s -C 17 -S $S -P $p -I $insert -r $r -f $trace_path/$trace"
                        sleep 1
                    done
                done
            done
        done
    done
done
```

Also, I calculate the Meta_cost and show the Meta cost in the table.

The final result for gcc trace is this:

![image-20240217215035178](/Users/pb/Documents/My-study-notes/pictures/image-20240217215035178.png)

The selected configuration is as follows:

| C1   | B1   | S1   | C2   | B2   | S2   | Prefetch Policy | Replacement Policy | Insertion Policy | Meta_cost | L1_AAT |
| ---- | ---- | ---- | ---- | ---- | ---- | --------------- | ------------------ | ---------------- | --------- | ------ |
| 15   | 7    | 4    | 17   | 7    | 4    | +1 prefetcher   | LRU                | LIP.             | 67328     | 2.043  |



## For leela.trace:

I run the combination of (b1,s1,b2,s2,prefetch_policy, Replacement_policy and insertion policy) for leela.trace:

```shell
b_values=(5 6 7)
s_values=(0 0 0 0 1 1 1 1 2 2 2 2 3 3 3 3 4 4 4 4 5 5 5 5)  
S_values=(2 3 4 5 2 3 4 5 2 3 4 5 3 4 5 6 4 5 6 7 5 6 7 8)  

insert_policy=(mip lip)
replacement_policy=(lru lfu)
prefetch_mode=(0 1 2)
trace_files=("leela.trace")  
trace_path="./traces"
for b in "${b_values[@]}"; do
    for i in {0..11}; do
        s=${s_values[i]}
        S=${S_values[i]}
        for trace in "${trace_files[@]}"; do
            for insert in "${insert_policy[@]}"; do
                for r in "${replacement_policy[@]}"; do
                    for p in "${prefetch_mode[@]}"; do
                        ./cachesim -c 15 -b "$b" -s "$s" -C 17 -S "$S" -P "$p" -I "$insert" -r "$r" -f "$trace_path/$trace"
                        echo "Ran: -c 15 -b $b -s $s -C 17 -S $S -P $p -I $insert -r $r -f $trace_path/$trace"
                        sleep 1
                    done
                done
            done
        done
    done
done

```

I prioritized L1 Average Access Time (AAT) in analysis, sorting the results in ascending order. 

The table present the top 30 configurations with the lowest L1 AAT for Leela trace.

<img src="/Users/pb/Documents/My-study-notes/pictures/image-20240217225427346.png" alt="image-20240217225427346" style="zoom: 50%;" />

The selected configuration is as follows:

| C1   | B1   | S1   | C2   | B2   | S2   | Prefetch Policy | Replacement Policy | Insertion Policy | Meta_cost | L1_AAT |
| ---- | ---- | ---- | ---- | ---- | ---- | --------------- | ------------------ | ---------------- | --------- | ------ |
| 15   | 7    | 2    | 17   | 7    | 2    | +1 prefetcher   | LRU                | MIP              | 64768     | 1.952  |



## For linpack.trace:

I run the combination of (b1,s1,b2,s2,prefetch_policy, Replacement_policy and insertion policy) for linpack.trace:

```shell
b_values=(5 6 7)
s_values=(0 0 0 0 1 1 1 1 2 2 2 2 3 3 3 3 4 4 4 4 5 5 5 5)  
S_values=(2 3 4 5 2 3 4 5 2 3 4 5 3 4 5 6 4 5 6 7 5 6 7 8)  

insert_policy=(mip lip)
replacement_policy=(lru lfu)
prefetch_mode=(0 1 2)
trace_files=("linpack.trace")  
trace_path="./traces"
for b in "${b_values[@]}"; do
    for i in {0..11}; do
        s=${s_values[i]}
        S=${S_values[i]}
        for trace in "${trace_files[@]}"; do
            for insert in "${insert_policy[@]}"; do
                for r in "${replacement_policy[@]}"; do
                    for p in "${prefetch_mode[@]}"; do
                        ./cachesim -c 15 -b "$b" -s "$s" -C 17 -S "$S" -P "$p" -I "$insert" -r "$r" -f "$trace_path/$trace"
                        echo "Ran: -c 15 -b $b -s $s -C 17 -S $S -P $p -I $insert -r $r -f $trace_path/$trace"
                        sleep 1
                    done
                done
            done
        done
    done
done

```

I prioritized L1 Average Access Time (AAT) in analysis, sorting the results in ascending order. 

The table present the top 30 configurations with the lowest L1 AAT for linpack trace.

![image-20240217230112794](/Users/pb/Documents/My-study-notes/pictures/image-20240217230112794.png)

The selected configuration is as follows:

| C1   | B1   | S1   | C2   | B2   | S2   | Prefetch Policy     | Replacement Policy | Insertion Policy    | Meta_cost | L1_AAT |
| ---- | ---- | ---- | ---- | ---- | ---- | ------------------- | ------------------ | ------------------- | --------- | ------ |
| 15   | 6    | 2    | 17   | 6    | 3    | Prefetcher disabled | LRU                | Prefetcher disabled | 131584    | 10.971 |



## For matmul_naive.trace:

I run the combination of (b1,s1,b2,s2,prefetch_policy, Replacement_policy and insertion policy) for matmul_naive.trace:

```shell
b_values=(5 6 7)
s_values=(0 0 0 0 1 1 1 1 2 2 2 2 3 3 3 3 4 4 4 4 5 5 5 5)  
S_values=(2 3 4 5 2 3 4 5 2 3 4 5 3 4 5 6 4 5 6 7 5 6 7 8)  

insert_policy=(mip lip)
replacement_policy=(lru lfu)
prefetch_mode=(0 1 2)
trace_files=("matmul_naive.trace")  
trace_path="./traces"
for b in "${b_values[@]}"; do
    for i in {0..11}; do
        s=${s_values[i]}
        S=${S_values[i]}
        for trace in "${trace_files[@]}"; do
            for insert in "${insert_policy[@]}"; do
                for r in "${replacement_policy[@]}"; do
                    for p in "${prefetch_mode[@]}"; do
                        ./cachesim -c 15 -b "$b" -s "$s" -C 17 -S "$S" -P "$p" -I "$insert" -r "$r" -f "$trace_path/$trace"
                        echo "Ran: -c 15 -b $b -s $s -C 17 -S $S -P $p -I $insert -r $r -f $trace_path/$trace"
                        sleep 1
                    done
                done
            done
        done
    done
done

```

I prioritized L1 Average Access Time (AAT) in analysis, sorting the results in ascending order. 

The table present the top 30 configurations with the lowest L1 AAT for matmul_naive trace.

![image-20240217230532089](/Users/pb/Documents/My-study-notes/pictures/image-20240217230532089.png)

The selected configuration is as follows:

| C1   | B1   | S1   | C2   | B2   | S2   | Prefetch Policy | Replacement Policy | Insertion Policy | Meta_cost | L1_AAT |
| ---- | ---- | ---- | ---- | ---- | ---- | --------------- | ------------------ | ---------------- | --------- | ------ |
| 15   | 7    | 2    | 17   | 7    | 3    | +1 prefetcher   | LFU                | MIP              | 65792     | 3.894  |



## For matmul_tiled.trace

I run the combination of (b1,s1,b2,s2,prefetch_policy, Replacement_policy and insertion policy) for matmul_tiled.trace:

```shell
b_values=(5 6 7)
s_values=(0 0 0 0 1 1 1 1 2 2 2 2 3 3 3 3 4 4 4 4 5 5 5 5)  
S_values=(2 3 4 5 2 3 4 5 2 3 4 5 3 4 5 6 4 5 6 7 5 6 7 8)  

insert_policy=(mip lip)
replacement_policy=(lru lfu)
prefetch_mode=(0 1 2)
trace_files=("matmul_tiled.trace")  
trace_path="./traces"
for b in "${b_values[@]}"; do
    for i in {0..11}; do
        s=${s_values[i]}
        S=${S_values[i]}
        for trace in "${trace_files[@]}"; do
            for insert in "${insert_policy[@]}"; do
                for r in "${replacement_policy[@]}"; do
                    for p in "${prefetch_mode[@]}"; do
                        ./cachesim -c 15 -b "$b" -s "$s" -C 17 -S "$S" -P "$p" -I "$insert" -r "$r" -f "$trace_path/$trace"
                        echo "Ran: -c 15 -b $b -s $s -C 17 -S $S -P $p -I $insert -r $r -f $trace_path/$trace"
                        sleep 1
                    done
                done
            done
        done
    done
done

```

I prioritized L1 Average Access Time (AAT) in analysis, sorting the results in ascending order. 

The table present the top 30 configurations with the lowest L1 AAT for matmul_tiled trace.

![image-20240217230733791](/Users/pb/Documents/My-study-notes/pictures/image-20240217230733791.png)

The selected configuration is as follows:

| C1   | B1   | S1   | C2   | B2   | S2   | Prefetch Policy | Replacement Policy | Insertion Policy | Meta_cost | L1_AAT |
| ---- | ---- | ---- | ---- | ---- | ---- | --------------- | ------------------ | ---------------- | --------- | ------ |
| 15   | 7    | 2    | 17   | 7    | 3    | +1 prefetcher   | LRU                | MIP              | 65792     | 2.059  |



## For mcf.trace:

I run the combination of (b1,s1,b2,s2,prefetch_policy, Replacement_policy and insertion policy) for mcf trace:

```shell
b_values=(5 6 7)
s_values=(0 0 0 0 1 1 1 1 2 2 2 2 3 3 3 3 4 4 4 4 5 5 5 5)  
S_values=(2 3 4 5 2 3 4 5 2 3 4 5 3 4 5 6 4 5 6 7 5 6 7 8)  

insert_policy=(mip lip)
replacement_policy=(lru lfu)
prefetch_mode=(0 1 2)
trace_files=("mcf.trace")  
trace_path="./traces"
for b in "${b_values[@]}"; do
    for i in {0..11}; do
        s=${s_values[i]}
        S=${S_values[i]}
        for trace in "${trace_files[@]}"; do
            for insert in "${insert_policy[@]}"; do
                for r in "${replacement_policy[@]}"; do
                    for p in "${prefetch_mode[@]}"; do
                        ./cachesim -c 15 -b "$b" -s "$s" -C 17 -S "$S" -P "$p" -I "$insert" -r "$r" -f "$trace_path/$trace"
                        echo "Ran: -c 15 -b $b -s $s -C 17 -S $S -P $p -I $insert -r $r -f $trace_path/$trace"
                        sleep 1
                    done
                done
            done
        done
    done
done

```

I prioritized L1 Average Access Time (AAT) in analysis, sorting the results in ascending order. 

The table present the top 30 configurations with the lowest L1 AAT for mcf trace.

![image-20240217231005494](/Users/pb/Documents/My-study-notes/pictures/image-20240217231005494.png)

The selected configuration is as follows:

| C1   | B1   | S1   | C2   | B2   | S2   | Prefetch Policy | Replacement Policy | Insertion Policy | Meta_cost | L1_AAT |
| ---- | ---- | ---- | ---- | ---- | ---- | --------------- | ------------------ | ---------------- | --------- | ------ |
| 15   | 7    | 2    | 17   | 7    | 5    | +1 prefetcher   | LRU                | MIP              | 67840     | 2.070  |



## Appendix:

The python code to anaylsis the output file and generate table:

```python
def parse_cache_settings(file_path):
    with open(file_path, 'r') as file:
        lines = file.readlines()
    return [l.rstrip("\n") for l in lines if len(l) >= 5]

def parse_string(data_str):
    parsed_values = {
        'c1': None,
        'b1': None,
        's1': None,
        'c2': None,
        'b2': None,
        's2': None,
        'prefetch': None,
        'rp': None,
        'IP': None,
    }
    sections = data_str.split('. ')
    l1_values = sections[0].split('(')[1].split(')')[0].split(',')
    parsed_values['c1'] = int(l1_values[0])
    parsed_values['b1'] = int(l1_values[1])
    parsed_values['s1'] = int(l1_values[2])
    parsed_values['rp'] = sections[1].split('.')[0]
    l2_values = sections[3].split('(')[1].split(')')[0].split(',')
    parsed_values['c2'] = int(l2_values[0])
    parsed_values['b2'] = int(l2_values[1])
    parsed_values['s2'] = int(l2_values[2])
    
    prefetch_value = sections[5]
    parsed_values['prefetch'] = sections[5]
    
    parsed_values['IP'] = sections[-1]
    return parsed_values

def compute_meta_data(c1,b1,s1,c2,b2,s2):
    meta1 = 2**(c1-b1)*(64-(c1-s1)+2)
    meta2 = 2**(c2-b2)*(64-(c2-s2)+1)
    return meta1 + meta2

  
from prettytable import PrettyTable

file_dict = {}
file_path = './pb_out/traces/mcf.trace.txt'
file_list = parse_cache_settings(file_path)
for i in range(0, len(file_list), 2):
    num_l = file_list[i+1].split()[0]
    file_dict[file_list[i]] = num_l

data_list = []
for line in file_dict.keys():
    line_dict = parse_string(line)
    parsed_data = [l for l in line_dict.values()]
 		parsed_data.append(compute_meta_data(line_dict['c1'],line_dict['b1'], line_dict['s1'] \ 			                         			        				  ,line_dict['c2'],line_dict['b2'],line_dict['s2'])) 
    parsed_data.append(file_dict[line])
    data_list.append(parsed_data)
    
table = PrettyTable()

sorted_data_lists_by_last_element = sorted(data_list, key=lambda x: x[-1])

column_names = [f"Column {i+1}" for i in range(len(data_list[0]))]
column_names = ["C1","B1", "S1","C2","B2","S2","Prefetch Policy","Replace Policy","Insertion Policy","Meta_cost","L1_AAT"]
table.field_names = column_names

for one_line in sorted_data_lists_by_last_element[:30]:
    table.add_row(one_line)

print(table)
```

The shell script to generate out for every trace:

```shell
for script in valid_grad_*.sh; do
    ./"$script" &
done

wait
```

