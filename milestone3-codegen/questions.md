# Milestone

#### My menu entries keep dissapearing

This is due to a bug in one of the dependencies of spoofax, which sometimes trows a NullPointerException.
There is no known solution at the time, but for some people upgrading from Eclipse 4.2 to 4.3 decreased the problem occurances.
As a workaround, you can restart eclipse with File->Restart. Eclipse should restore all windows etc. and your menu should be complete again.
