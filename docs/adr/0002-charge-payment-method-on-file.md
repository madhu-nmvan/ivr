# Pay Premium charges the Payment Method on File; no card capture by voice

The IVR never asks for card or bank numbers; Pay Premium charges the full Amount Due to the Credit Card or PAD already on the Policy. Capturing card digits over the phone would pull the Call path (speech-to-text, transcripts, Call Records) into PCI DSS scope. In the draft the charge is mocked; Moneris is the intended processor later. Payment on a Lapsed Policy is refused, so the IVR never reinstates a Policy.
