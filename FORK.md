# LiveSplit integration branch

This branch starts at upstream softbuffer 0.4.8 (`d871852faa1137d4615b99b9ad31c3ba80f345b5`).
It keeps the changes needed by LiveSplit's software presenter in separate commits:

1. Allocate Wayland shared-memory buffers as ARGB8888, including after resize,
   so premultiplied alpha reaches the compositor.
2. Expose `Surface::uses_alpha()` so the presenter can distinguish those Wayland
   buffers and existing X11 depth-32 visuals from XRGB surfaces. The query uses
   state already collected during surface creation and performs no protocol calls.

These changes extend upstream's pixel contract. When `uses_alpha()` is true,
callers must supply premultiplied `0xAARRGGBB` pixels and initialize alpha to 255
for opaque content. Other surfaces retain upstream's zero-high-byte contract.
The Wayland change affects every surface and needs API/design review before
upstreaming; splitting it from the query does not imply upstream acceptance.

Applications should depend on this public branch through an exact Git revision
and commit their Cargo lockfile. Every machine can then fetch the same source
without a local checkout, vendored directory, or Cargo path override.
