# DISK SCHEDULING 
```bash
"""
Disk Scheduling Algorithms
Supports: FCFS, SSTF, SCAN, C-SCAN, LOOK, C-LOOK
"""

def fcfs(requests, head):
    """First Come First Serve"""
    sequence = [head] + requests
    total = sum(abs(sequence[i] - sequence[i-1]) for i in range(1, len(sequence)))
    return requests[:], total


def sstf(requests, head):
    """Shortest Seek Time First"""
    remaining = requests[:]
    sequence = []
    current = head
    total = 0

    while remaining:
        closest = min(remaining, key=lambda x: abs(x - current))
        total += abs(closest - current)
        current = closest
        sequence.append(closest)
        remaining.remove(closest)

    return sequence, total


def scan(requests, head, disk_size=200, direction="right"):
    """SCAN (Elevator) Algorithm"""
    left = sorted([r for r in requests if r < head], reverse=True)
    right = sorted([r for r in requests if r >= head])

    sequence = []
    total = 0
    current = head

    if direction == "right":
        for r in right:
            total += abs(r - current)
            current = r
            sequence.append(r)
        if left:
            total += abs(disk_size - 1 - current)
            current = disk_size - 1
            for r in left:
                total += abs(r - current)
                current = r
                sequence.append(r)
    else:
        for r in left:
            total += abs(r - current)
            current = r
            sequence.append(r)
        if right:
            total += abs(current)
            current = 0
            for r in right:
                total += abs(r - current)
                current = r
                sequence.append(r)

    return sequence, total


def cscan(requests, head, disk_size=200):
    """C-SCAN (Circular SCAN) Algorithm"""
    left = sorted([r for r in requests if r < head])
    right = sorted([r for r in requests if r >= head])

    sequence = []
    total = 0
    current = head

    for r in right:
        total += abs(r - current)
        current = r
        sequence.append(r)

    if left:
        total += abs(disk_size - 1 - current) + (disk_size - 1)
        current = 0
        for r in left:
            total += abs(r - current)
            current = r
            sequence.append(r)

    return sequence, total


def look(requests, head, direction="right"):
    """LOOK Algorithm"""
    left = sorted([r for r in requests if r < head], reverse=True)
    right = sorted([r for r in requests if r >= head])

    sequence = []
    total = 0
    current = head

    if direction == "right":
        for r in right:
            total += abs(r - current)
            current = r
            sequence.append(r)
        for r in left:
            total += abs(r - current)
            current = r
            sequence.append(r)
    else:
        for r in left:
            total += abs(r - current)
            current = r
            sequence.append(r)
        for r in right:
            total += abs(r - current)
            current = r
            sequence.append(r)

    return sequence, total


def clook(requests, head):
    """C-LOOK Algorithm"""
    left = sorted([r for r in requests if r < head])
    right = sorted([r for r in requests if r >= head])

    sequence = []
    total = 0
    current = head

    for r in right:
        total += abs(r - current)
        current = r
        sequence.append(r)

    if left:
        total += abs(left[-1] - current) + abs(left[0] - left[-1])
        current = left[0]
        sequence.append(left[0])
        for r in left[1:]:
            total += abs(r - current)
            current = r
            sequence.append(r)

    return sequence, total


def run_all(requests, head, disk_size=200):
    algorithms = {
        "FCFS":   fcfs(requests, head),
        "SSTF":   sstf(requests, head),
        "SCAN":   scan(requests, head, disk_size),
        "C-SCAN": cscan(requests, head, disk_size),
        "LOOK":   look(requests, head),
        "C-LOOK": clook(requests, head),
    }

    print(f"\n{'='*60}")
    print(f"  Disk Scheduling Comparison")
    print(f"  Head: {head} | Disk Size: {disk_size}")
    print(f"  Requests: {requests}")
    print(f"{'='*60}")
    print(f"{'Algorithm':<10} {'Seek Sequence':<35} {'Total Seeks'}")
    print(f"{'-'*60}")

    for algo, (seq, total) in algorithms.items():
        print(f"{algo:<10} {str(seq):<35} {total}")

    print(f"{'='*60}\n")
    return algorithms


if __name__ == "__main__":
    requests = [98, 183, 37, 122, 14, 124, 65, 67]
    head = 53
    disk_size = 200

    results = run_all(requests, head, disk_size)
```
