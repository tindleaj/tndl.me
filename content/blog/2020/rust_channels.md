+++
title = "Figuring out Rust channels"
draft = true
+++

Recently I've been working on a small [Rust project for generating ambient soundscapes](). It scratches an itch of mine, mainly the want for an offline, standalone app that mimics [Noisli]() or [MyNoise](). It was also a great opportunity to learn a little bit about audio, concurrency, and building GUIs with Rust.

In this post I want to focus specifically on _concurrency_, since it's one of the major selling points of Rust and something I didn't have much experience with coming from the world of JavaScript.
