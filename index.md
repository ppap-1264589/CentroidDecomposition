# [Trang chủ](https://ppap-1264589.github.io/interesting-solution)

## Ý nghĩa

Thực hiện một số bài toán có thao tác update mang tính bao trọn lên tất cả các nút trong cây

## Tài liệu

[VNOJ](https://oj.vnoi.info/post/72-noobcpp)

## Bài toán cụ thể

[QTREE5](https://www.spoj.com/problems/QTREE5)

Bạn có thể download bộ test tại [đây](https://drive.google.com/drive/folders/1k2WOkaA5yQ4Vj8AUQA_p5CsfF1OnpOdj?usp=sharing)

## Code: [QTREE5.cpp](https://ideone.com/WYZlyz)

```c++
//QTREE5 problem
#include <bits/stdc++.h>
#define up(i,a,b) for (int i = (int)a; i <= (int)b; i++)
#define ep emplace_back
#define pii pair<int, int>
#define f first
#define s second
using namespace std;
 
const int maxn = 1e5 + 10;
const int LIM = 1061109567;
static vector<int> G[maxn];
static int n,q;
static int Tsize[maxn];
 
static bool locked[maxn];
static int subsize(int u, int PAR){
    Tsize[u] = 1;
    for (auto& v : G[u]){
        if (v == PAR || locked[v]) continue;
        Tsize[u] += subsize(v, u);
    }
    return Tsize[u];
}
 
static int Centroid(int u, int PAR, int n){
    for (auto& v : G[u]){
        if (v == PAR || locked[v]) continue;
        if (Tsize[v] * 2 > n) return Centroid(v, u, n);
    }
    return u;
}
 
static int depth[maxn];
static int tin[maxn], node[maxn << 1];
static int tdfs;
static void DFS(int u, int PAR){
    node[++tdfs] = u;
    tin[u] = tdfs;
    for (auto& v : G[u]){
        if (v == PAR || locked[v]) continue;
        depth[v] = depth[u] + 1;
        DFS(v, u);
        node[++tdfs] = u;
    }
}
 
const int LOG = log2(maxn << 1) + 1;
static int sp[LOG][maxn << 1];
 
static bool LESS(int u, int v){
    return depth[u] < depth[v];
}
static void make_sparse(int n){
    up(i,1,n) sp[0][i] = node[i];
    for (int i = 1; (1 << i) <= n; i++){
        for (int j = 1; j + (1 << i) <= n; j++){
            int& d1 = sp[i-1][j];
            int& d2 = sp[i-1][j + (1 << (i-1))];
            if (LESS(d1, d2)) sp[i][j] = d1;
            else sp[i][j] = d2;
        }
    }
}
 
static int LCA(int u, int v){
    int l = tin[u];
    int r = tin[v];
    if (l > r) swap(l, r);
    int k = 31 - __builtin_clz(r - l + 1);
    int& d1 = sp[k][l];
    int& d2 = sp[k][r - (1 << k) + 1];
    if (LESS(d1, d2)) return d1;
    return d2;
}
 
static int dist(int u, int v){
    return depth[u] + depth[v] - 2*depth[LCA(u, v)];
}
 
static int par[maxn];
static void build_CD(int u, int PAR){
    int n = subsize(u, PAR);
    int C = Centroid(u, PAR, n);
    par[C] = PAR;
    locked[C] = true;
 
    for (auto& v : G[C]){
        if (locked[v]) continue;
        build_CD(v, C);
    }
}
 
static priority_queue<pii, vector<pii>, greater<pii> > res[maxn];
static bool dd[maxn];
 
static void toggle(int x){
    int u = x;
    if (dd[x] == 0){
        while (u){
            res[u].push(make_pair(dist(u, x), x));
            u = par[u];
        }
    }
    dd[x] = !dd[x];
}
 
static int localmin(int u){
    while (!res[u].empty()){
        int v = res[u].top().s;
        int w = res[u].top().f;
        if (!dd[v]) res[u].pop();
        else return w;
    }
    return LIM;
}
 
static int get(int x){
    int fin = localmin(x);
    int u = x;
    while (u){
        fin = min(fin, localmin(u) + dist(u, x));
        u = par[u];
    }
    return fin;
}
 
static void solve(){
    cin >> n;
    up(i,1,n-1){
        int u,v;
        cin >> u >> v;
        G[u].ep(v);
        G[v].ep(u);
    }
 
    DFS(1, 0);
    make_sparse(tdfs);
    build_CD(1, 0);
 
    cin >> q;
    while (q--){
        int type, x;
        cin >> type >> x;
        if (type == 0) toggle(x);
        else {
            int result = get(x);
            cout << (result > maxn ? -1 : result) << "\n";
        }
    }
}
 
signed main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    #define Task "QTREE5"
    if (fopen(Task".inp", "r")){
        freopen(Task".inp", "r", stdin);
        freopen(Task".out", "w", stdout);
    }
 
    solve();
}
```
