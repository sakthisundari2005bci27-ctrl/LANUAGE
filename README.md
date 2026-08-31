#include <iostream>
#include <vector>

using namespace std;

// The sequence generation function
inline unsigned long long get_next(unsigned long long current, unsigned long long P, unsigned long long Q) {
    const unsigned long long MOD = 1ULL << 31;
    return (current * P + Q) % MOD;
}

int main() {
    // Optimize standard input/output streams for speed
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    unsigned long long N, S, P, Q;
    if (!(cin >> N >> S >> P >> Q)) return 0;

    const unsigned long long MOD = 1ULL << 31;
    S %= MOD;

    // Phase 1: Brent's Algorithm to find the cycle length (lambda)
    unsigned long long power = 1;
    unsigned long long lambda = 1;
    unsigned long long tortoise = S;
    unsigned long long hare = get_next(S, P, Q);

    unsigned long long total_elements_checked = 1;

    while (tortoise != hare && total_elements_checked < N) {
        if (power == lambda) {
            tortoise = hare;
            power *= 2;
            lambda = 0;
        }
        hare = get_next(hare, P, Q);
        lambda++;
        total_elements_checked++;
    }

    // If we processed N elements without falling into a full cycle
    if (total_elements_checked < N) {
        // Phase 2: Find the distance to the start of the cycle (mu)
        tortoise = S;
        hare = S;
        for (unsigned long long i = 0; i < lambda; i++) {
            hare = get_next(hare, P, Q);
        }

        unsigned long long mu = 0;
        while (tortoise != hare) {
            tortoise = get_next(tortoise, P, Q);
            hare = get_next(hare, P, Q);
            mu++;
        }

        // Total unique elements is the pre-period length (mu) + period length (lambda)
        // capped at total available sequence length N
        unsigned long long distinct_count = mu + lambda;
        if (distinct_count > N) {
            distinct_count = N;
        }
        cout << distinct_count << "\n";
    } else {
        // If the sequence boundary N was hit before finding a cycle
        cout << total_elements_checked << "\n";
    }

    return 0;
}
