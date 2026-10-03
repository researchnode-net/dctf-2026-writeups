# sot-chall pirate io writeup

## recon

# a pirate .io game at sot-chall.abcjr.dev
# index.html pins a release manifest at https://sot-chall-test-versions.abcjr.dev/releases/prod/v0.1.1-.../release.json, which lists the files: entry js, wasm, pipworld2.dat, assets.idx.

# the game server is a websocket at wss://sot-chall-test-prod-server.abcjr.dev/ws, and there's a /healthz on it that dumps worker/room stats.

# decompiled the wasm with wabt (wasm-decompile)

## flag 1: clairvoyance (wallhack)

# the assets are packed .pak files listed in assets.idx. there are 5 images but the html only renders 4. the 5th, dev_tutorial, is a screenshot of the dev overlay (needed further ig)

# the flag comes in two halves.

# first half is from the game itself. bottles float in the sea and when u pick it up u get the message: The waves whisper: DCTF{W4llh4ck1.

# second half hides in the reef rocks each rock carries a variant byte, reef rows decode to TRYHARDER!, but one reef row sits next to an invisible wall and the rocks in that row decode to N6_1s_s0_m3Ta}.

# DCTF{W4llh4ck1N6_1s_s0_m3Ta}

## flag 2: yore (old release)

# the dev_tutorial image shows an old release id in the overlay text: v0.0.9-d2bad05a4314-20260914T130501Z. that build was the dev variant, and its files are still on the versions server at https://sot-chall-test-versions.abcjr.dev/releases/dev/v0.0.9-.../release.json.

# grab its assets.idx. it has an extra pak the prod build doesn't ship, the dev pack, and it lists a flag.txt asset. download it with curl .../releases/dev/v0.0.9-.../dev.pak.

# the pak is a tiny PPAK container, and flag.txt sits in plaintext inside it.

# DCTF{M4yb3_cl34n_y0ur_S3_buCk375_fr0m_t1m3_t0_71m3}

## flag 3: king of the hill (trust the client)

# there's a flag zone in the middle of the map with a pirate flag on it. you can't sail into it normally, even as dread pirate. .

# the bug: throttle is a float the client sends and the server trusts. the browser caps it, but nothing server-side checks it. speed per tick is throttle/30, and the zone is only tested against your position at the END of the tick.

# so send throttle 20000. DCTF{l3550n_Numb3r_0n3_n3v3r_7rust_7He_cl13Nt}.


## gg