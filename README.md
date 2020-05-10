buslogger
===========

[buslogger](https://github.com/apgoetz/buslogger) is a small helper
library
for [embedded-hal](https://github.com/rust-embedded/embedded-hal) rust
applications. It helps with debugging bus interfaces by logging each
read and write transaction to a separate logging device.

Currently, it only supports I2C busses, but support is planned for
other bus types.

buslogger works by moving in a struct that implements the i2c traits,
as well as a struct that implements the core::fmt::Write trait. It
then returns a new i2c struct that also implements the i2c traits, in
a similiar fashion to
the [shared_bus](https://github.com/Rahix/shared-bus) crate.

Example usage:

```rust
use buslogger::BusLogger;
let i2c = /* some struct that implements read / write / write_read traits */;
let serial = /* some struct that implements core::fmt::Write */;
let i2c = BusLogger::new(serial,i2c);
let mut buf : [u8 ; 1] = [0;1];
// use the i2c device like normal
i2c.write(0x42, &[0x40,0x80]).ok();
i2c.write_read(0x42, &[0x40], &mut buf).ok();
i2c.read(0x42, &mut buf).ok();
i2c.write_read(0x42, &[0x40], &mut buf).ok(); //assume this fails with error i2c::Error::Nack
```

Example output you would see on the trace

```
a[42] w[40, 80]
a[42] w[00] r[01]
a[42] r[00]
a[42] w[01] Nack
```
